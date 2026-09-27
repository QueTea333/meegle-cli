# Update work item

> **CRITICAL** — Before starting, MUST use the Read tool to read `../SKILL.md`, which contains pre-checking, authorization process, command parameter reference, field value format, general specifications and error handling.

This skill is used to update field values ​​or role members of work items in Feishu projects, and the entire process is automated. Excludes status transfer and node transfer (responsible by other Skills).

---

## Execution process

### STEP 1 — Locate work items and extract modification intent

Extract from user input:
- **Target Work Item** — URL, work item ID, or name
- **Modify content** — Which fields should be changed to what values?

> **URL processing**: The user must first adjust `url decode` after giving a URL. Only `url_kind == workitem_detail` can enter this SOP; for other kinds, please press [url-kinds.md](url-kinds.md) to refuse or ask. After getting `simple_name` and `work_item_id`, you must call `project search` to convert `simple_name` into the authoritative `project_key` (there may be multiple spaces with the same name). **It is forbidden** to intercept the path segment from the URL as a parameter. The `work_item_id` parameter must be of type string.

🚨 **Get work item type (very important)**: All subsequent queries (field configuration, role configuration, etc.) are strongly dependent on `work_item_type`. If the user does not explicitly inform the type, **must first call `workitem get` to obtain the real `work_item_type` of the instance (`work_item_type.key` in the return body)** and must not guess it as a story or issue.

### STEP 2 — Parallel query configuration

Depending on the content to be modified, **Initiate in parallel within one round**:

| Call | Condition | Description |
|------|------|------|
| `workitem meta-fields(field_query="field name")` | Involves field modification | For ordinary fields, use `field_query` for fuzzy search or `field_keys` for exact matching, which prohibits page-by-page traversal without filtering; for attachment field disambiguation, use [attachment.md](attachment.md) to use type filtering and turn the page to `has_more=false` |
| `workitem meta-roles` | Involves role modification | Get role key |
| `user search` | Personnel fields involved | Convert names to userkey in batches |

🚨 **Efficiency Requirements**: Ordinary configurations must perform one round of concurrent queries, and perform updates directly in the next round. Serial queries one by one are prohibited. The only exception is attachment field disambiguation: the first round of filtering queries by type; if `has_more=true`, serial paging must continue to collect all candidates before updating.

**Attachment field access control**: As long as the user requests to upload an attachment, all `file` and `multi-file` fields under the current space and real work item type must be queried in real time. When the user specifies a field name or key, do an exact match; when the user only says "attachment" and multiple candidates appear, list the field name, key, and type and let the user choose. It is forbidden to take the first one by default, it is forbidden to reuse the attachment field in the historical session, and it is forbidden to hard-code `attachment` or `multi_attachment`. After the target is determined, the same field key must be used for uploading, updating, and reading back. See [attachment.md](attachment.md) for the complete agreement.

### STEP 3 — Convert field value

Convert the natural language value given by the user into API format (for the complete format, see the main document [SKILL.md](../SKILL.md) "Field Value Format"):

> 🚨 **Key Convention**: `field_value` protocol layer is **STRING**. Scalars are directly stringified; arrays/objects must be JSON.stringify**, otherwise `need STRING type, but got: LIST` will be reported.

| field_type | Update scene differentiation prompts |
|---|---|
| `user` | Name → `user search` → single userkey string; when multiple people have the same name, the user says "assign it to myself" using `current_login_user()`, otherwise **must confirm** |
| `signal` | `option_id` string (based on `options[].option_id` of `workitem meta-fields`; `"true"`/`"false"`/`"null"` is not accepted) |
| `workitem_related_multi_select` | **stringified** ID array, **forbidden to write own ID** (prevent circular reference, trigger `exists loop` error) |
| `group_type` | The group pulling method is a logical field, reading returns the discrimination key `value`, writing the protocol discrimination key `type` (`auto`/`bind`/`disabled`), **reading and writing asymmetry** |
| `multi-file` | Press [attachment.md](attachment.md) to complete "local file verification → field disambiguation → object storage upload → field binding → acceptance after writing"; when appending, the current complete array must be read and retained, and then appended one by one |
| `file` | Treated according to the compatibility rules of [attachment.md](attachment.md); legacy or single-attachment fields must not be assumed to support multiple attachments, nor may they share the unvalidated multi-attachment writing protocol with `multi-file` |

> For details on the writing format of other common field types (text / number / bool / multi-user / date / schedule / precise_date / select series / multi-text, etc.), please refer to the "Field Value Format" chapter of the main document [SKILL.md](../SKILL.md).

> **When the user provides a work item name instead of an ID**, it is processed according to the [field-value-extras.md](field-value-extras.md) "Associated work item name → ID conversion" complete process (get target constraints → `workitem query` search → disambiguation → write by type).

### STEP 4 — Perform update

```bash
meegle workitem update --work-item-id work item ID --project-key space key --role-operate '{{role_operate}}' --fields '[{"field_key":"priority","field_value":"option_id"}]' --format json
```

**Role operation** via `role_operate` parameter:
```json
[{"op": "add", "role_key": "role key", "user_keys": ["userkey1"]}]
```

**Group mode update (`group_type` logical field)**: The server merges `group_id` / `chat_group` into the `group_type` single field, and uniformly reads and writes through it - ** It is forbidden to write `group_id` / `chat_group`** separately. Write protocol `field_value`:

```json
{"type": "auto" | "bind" | "disabled", "group_id": "oc_xxx"}
```

⚠️ **Reading and writing asymmetry**: The discriminating key returned by reading is `value` (such as `{"value":"auto","label":"automatic pull group","group_id":"oc_xxx"}`), and the discriminating key required by writing is `type`. You cannot directly throw the read structure back to update.

| `type` | Meaning | Whether to include `group_id` | Client pre-verification |
|---|---|---|---|
| `auto` | Automatically pull the group | Not allowed | With → Blocked (the server will reply `group_type conflicts with group_id: type=auto`) |
| `bind` | Bind an existing group | **Required**, non-empty `oc_xxx` (empty strings/pure spaces are also missing) | Missing or completely blank → blocked (the server will return `group_id is required when group_type=bind`) |
| `disabled` | Not allowed | Not allowed | With → Blocked (the server will reply `group_type conflicts with group_id: type=disabled`) |

Writing example (see [api-examples.md](api-examples.md) work item field for details):

```bash
meegle workitem update --work-item-id work item ID --project-key space key --role-operate '{{role_operate}}' --fields '[{"field_key":"group_type","field_value":"{\"type\":\"bind\",\"group_id\":\"oc_xxx\"}"}]' --format json
```

### STEP 5 — Return results

If attachments are involved, first call `workitem get` to read back the disambiguated target fields and check the requested file name and quantity one by one. Neither the upload interface nor the update interface returns success, and it cannot replace the post-write acceptance of the target field. After the acceptance is passed, the modified field, target attachment field name/key, actual file name and quantity will be displayed; when the page layout cannot be verified, it will only be declared that the API field was written successfully.

---

## Incremental append (Append SOP)

When users request to "append" or "add" content (rather than overwriting), they must follow:

**1. Get the old value** → Call `workitem get` or `workitem query` to check the current value

**2. Merge old and new values**:

| Field type | Merge method |
|---------|---------|
| Text (`text` / `multi-text`) | New text is spliced ​​after the old text |
| Multi-select enumeration (`multi-select`) | Old options + new options, `[{"option_id":"xxx"}, ...]` |
| Tree multi-select (`tree-multi-select`) | Pure string one-dimensional array `["id1", "id2"]`, deduplication |
| Related work items (`workitem_related_multi_select`) | Old ID + new ID, write after deduplication (can only bind instances in the same space or whitelisted space) |
| Multi-select people (`multi-user`) | Old userkey + new userkey, remove duplicates |
| Attachment (`multi-file`) | Press [attachment.md](attachment.md) to append one by one: read the target fields in each round, keep the complete object of the old attachment (including the `uid`, `url` and unknown fields supplemented by the server) as it is, only append a new object, and read back and accept it immediately after the entire stringify is written back. `update` has overwriting semantics. If the old value is not taken, historical attachments will be lost |

**3. Overwrite writing** → write the merged value through `workitem update`

### Adding and deleting roles

| Operations | `role_operate` parameters |
|------|-------------------|
| **Add role personnel** | `{"op": "add", "role_key": "xxx", "user_keys": ["userkey"]}` — No need to get the old value first |
| **Clear role personnel** (retain roles) | `{"op": "remove", "role_key": "xxx", "user_keys": []}` |
| **Delete role completely** | `{"op": "remove", "role_key": "xxx", "user_keys": ["_all"]}` |

### Dynamic MQL query append

When the user uses natural language conditions to request the addition of related items (such as "Append all requirements with names containing 'dependency' to pre-dependencies"):
1. Use `workitem query` to retrieve work items that meet the conditions
2. Extract ID list
3. Press the [Get Old Value → Merge → Write] process to append

---

## Boundary description

| Scene | Processing |
|------|------|
| **Node-level fields** (scheduling, rating, node leader) | Do not belong to this Skill, you need to use `workflow update-node`. If it is detected that the user wants to change the node field, automatically adjust `workflow update-node` |
| **Role Update** | Must pass `role_operate`, cannot be placed in `fields` |
| **Template switching** (modify template) | High-risk operation, this is the **only scenario where the user needs to be reminded to confirm** |

---

## Unwritable field types

The following fields do not support API writing. If encountered, they will be skipped directly and notified to the user:

| Type | Reason |
|------|------|
| `vote-boolean` (lightweight expression) | Counter, only page operations |
| `vote-option` / `vote-option-multi` (vote) | Does not support interface forgery |
| Calculated field | Automatically calculated by the system, read-only |

> **Compound details table already supports writing through `workitem update`**, but the two types of protocols are different: `compound_field` uses stringified action objects and locates rows with `group_uuid`; `multi_user_compound_field` uses stringified userkey map, and is fully covered. Before updating the multi-person compound field, you must first read the current value and retain all personnel and non-target data; this protocol only updates existing personnel in the current map. When the value is empty or the target personnel is not in the map, new personnel cannot be automatically added. It must be stopped and the user first configures the personnel range through the page. For details on the format, please refer to the chapter "Field Value Format → Composite Detailed List" in the main document [SKILL.md](../SKILL.md).

**Rich text and related fields**:
- **Rich text/multi-line text** → Directly pass Markdown string
- **Associated cloud documents** → URL array `["https://xxx.feishu.cn/docx/xxx"]`
- **Pre-dependency/associated work item** → work item ID, different spaces may require string or numeric format, switch immediately when type verification fails; when the user gives a name instead of an ID, press [field-value-extras.md](field-value-extras.md) "Associated work item name → ID conversion" for complete process processing
- **signal type** → `option_id` string (based on `options[].option_id` of `workitem meta-fields`)
- **Cascading options (tree-select)** → Only pass the `option_id` pure string; when reporting `not meeting the hierarchical configuration`, search the `children` tree to find the last leaf node, **display it to the user for selection**
- **Circular reference protection** → Before writing the associated field, the current work item's own ID must be checked**, and it is prohibited to write the own ID into the associated item, otherwise an `exists loop` (circular reference) error will be triggered.

---

## Automatic error recovery (self-healing mechanism)

> For general self-healing rules (format error, cascading level, illegal enumeration), see [error-handling.md](error-handling.md). The following are supplementary rules to this SOP:

| Error reporting characteristics | Self-healing actions |
|---------|---------|
| Field name cannot be matched | Use `field_query` to fuzzy search to find the best match |
| The enumeration value cannot be matched | Fuzzy matching option_name (inclusion relationship), if failed, all options will be listed |
| Role key is uncertain | Use `role_query` to fuzzy search |

---

## Circuit Breaker

> For general circuit breaker rules (space not found, insufficient permissions), see the main document [SKILL.md](../SKILL.md) "Error Handling". The following are supplementary rules to this SOP:

1. **Work item type not found**: `workitem meta-types` failed more than 3 times
2. **Extensive field conversion failure**: conversion failure ratio > 60%, terminate and list failed field details

---

## FAQ

| Problem | Solution |
|------|------|
| User did not specify a work item | Asking for ID or URL |
| Field name cannot be matched | `workitem meta-fields(field_query="keyword")` Fuzzy query |
| The enumeration value cannot match | Display all enumeration values ​​for the user to choose |
| Person name matched to multiple people | Display list allows users to specify, **No self-selection allowed** |
| The user wants to "append" rather than overwrite | Follow the incremental append SOP |
| The user wants to change the node field | Automatically switch to `workflow update-node` |
