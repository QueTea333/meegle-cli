#Create work item

> **CRITICAL** — Before starting, MUST use the Read tool to read `../SKILL.md`, which contains pre-checking, authorization process, command parameter reference, field value format, general specifications and error handling.

This skill is used to create work items (requirements, tasks, defects, etc.) in the Feishu project and execute the entire process automatically.

---

## Execution process

### STEP 1 — Extract intent

Extract from user input:
- **Space name**: Which project space
- **Work item type**: Requirement/Task/Defect/Other
- **Field value**: title, priority, person in charge, description, etc.
- **URL** (if any): adjust `url decode` for parsing first. When `url_kind` is not `workitem_create` / `workitem_detail`, press [url-kinds.md](url-kinds.md) to reject or ask; if hit, use the returned `simple_name` to replace the space name detection, and `work_item_type` to replace the type detection. **It is forbidden** to intercept the path segment from the URL as a parameter.


### STEP 2 — Confirm space and type

1. Use `project search` to verify the space → obtain `project_key`
2. Use `workitem meta-types` to get the type list → confirm `work_item_type`

> If there is a unique match, use it directly. If there are multiple matches, a list will be displayed for the user to choose. If there is no match, the user will be asked. **No guessing allowed. **

### STEP 3 — Collect metadata (parallel)

At the same time, the following calls are made:

| call | purpose |
|------|------|
| `workitem meta-create-fields` | Get the **required fields** meta-information of this type (pre-verification to prevent creation failure due to missing space custom required fields) |
| `workitem meta-fields(field_keys=["template"])` | **Get template ID** (required for creation) |
| `workitem meta-fields(field_query="field name mentioned by the user")` | Confirm the field key, type, and enumeration value (through `field_keys` exact matching or `field_query` fuzzy search to avoid missing custom fields due to paging during full pull) |
| `workitem meta-roles(page_num=1)` | Get role definition (if the user has specified roles such as person in charge) |

If the user mentions a person's name, call `user search` in parallel to convert it to userkey.

> By default, only current users are queried. Pass `--need-all-status=true` to `user search` only when the user explicitly requests to include non-employed personnel such as resignation or suspension, or when the employment result is empty and the user requests to continue searching.

**Attachment field access control**: As long as the user requests to upload attachments, they must additionally call `workitem meta-fields` to query all `file` and `multi-file` fields in the current space and type in real time. When the user specifies a field name or key, do an exact match; when the user only says "attachment" and multiple candidates appear, list the field name, key, and type and let the user choose. It is forbidden to take the first one by default, it is forbidden to reuse the attachment field in the historical session, and it is forbidden to hard-code `attachment` or `multi_attachment`. After the target is determined, the same field key must be used for uploading, creating and reading back. See [attachment.md](attachment.md) for the complete agreement.

### STEP 4 — Automatic matching template

Template enumeration value obtained according to STEP 3:
- **Only one template** → Auto-select
- **Multiple Templates** → Match the closest template name based on the keywords in the user description, and display a list for the user to choose if they cannot choose.
- **User explicitly specified template name** → Exact match

### STEP 5 — Automatically complete required fields and convert field values

**1. Auto-complete required fields**

Strictly compares the required fields returned by `workitem meta-create-fields` with the fields given by the user. Automatically generate sensible default values ​​for missing required fields (especially special required fields for space customization):
- Enumeration: the first item is taken by default
- Text: Fill in "Automatically generated"
- Boolean: fill in true

**2. Convert field value**

Convert the natural language value given by the user and the automatically generated default value into the format required by the API:

| Source | Convert |
|------|------|
| Person name | Call `user search` to batch convert to userkey |
| Enumeration value | Get option_id from option_name matching in field configuration options |
| Date | Convert to millisecond timestamp |
| Other types | Converted according to the "Field Value Format" specification of the main document [SKILL.md](../SKILL.md) |

**Format quick check** (create scene-specific constraints; for general types, see the "Field value format" chapter of the main document [SKILL.md](../SKILL.md)):

> 🚨 **Key Convention**: `field_value` protocol layer is **STRING**. Scalars are directly stringified; arrays/objects must be JSON.stringify first, otherwise `need STRING type, but got: LIST` will be reported.

| Field type | Differentiation tips |
|---------|-----------|
| `role_owners` | **Available only on creation**; stringified object array `"[{\"role\":\"<role_id>\",\"owners\":[\"<userkey>\"]}]"` |
| `signal` | option_id plain string `"<option_id>"` (based on `options[].option_id` of `workitem meta-fields`; `"true"`/`"false"`/`"null"` is not accepted) |
| `workitem_related_multi_select` | **stringified** ID array, **forbidden to write own ID** (anti-cyclic reference, trigger `exists loop`) |
| `multi-file` | Press [attachment.md](attachment.md) to complete "local file verification → field disambiguation → object storage upload → field binding → acceptance after writing". A single attachment can be written as it is created; multiple attachments are first written as they are created, and then read, merged and updated one by one after obtaining the work item ID to avoid `unique` verification and overwriting loss |
| `file` | Treated according to the compatibility rules of [attachment.md](attachment.md); legacy or single-attachment fields must not be assumed to support multiple attachments, nor may they share the unvalidated multi-attachment writing protocol with `multi-file` |

> For details on the writing format of other common field types (text / user / multi-user / date / schedule / precise_date / select series, etc.), please refer to the "Field Value Format" chapter of the main document [SKILL.md](../SKILL.md).

**Role settings** (on creation): Through the `role_owners` field in fields, the value is an array of stringified objects:

```json
{"field_key":"role_owners","field_value":"[{\"role\":\"<role_id>\",\"owners\":[\"<userkey>\"]}]"}
```

> **Role Supplement**: In addition to processing a few built-in role_owners fields in the `fields` array during creation, other custom roles (such as PO, PM, Tech Lead, etc.) must be additionally written using the `role_operate` parameter of `workitem update` after creation.

### STEP 6 — Create

```bash
meegle workitem create --work-item-type type key --fields '[{"field_key":"template","field_value":"template ID"},{"field_key":"name","field_value":"title"}]' --project-key space key --format json
```

🚨 **Batch creation**: When users require to create multiple work items in batches, they must **serially call** (request one by one), and high concurrency is prohibited to avoid triggering platform current limiting. Each field_value must conform to the STRING convention of "field value format" (scalar direct stringification; array/object JSON.stringify).

### STEP 7 — Confirm results

After successful creation, if attachments are involved, you must first call `workitem get` to read back the disambiguated target fields and check the requested file name and quantity one by one. The upload interface returns token and the creation interface returns success, neither of which can replace the post-write acceptance of the target field; reading back other attachment fields does not count as success.

After the acceptance is passed, the user is shown:
- Work item ID and name
- Link (if included in return)
- Summary of key fields that have been set
- Attachment target field name/key, actual file name and quantity; when the page layout cannot be verified, only declare that the API field is written successfully

---

## Special field writing rules

### Cascading options (Tree-select)

1. **Minimal format**: For the business line (`_business`) hierarchical radio-select field, `field_value` **only passes the `option_id` pure string**, do not pass the complex JSON of `value/label/children`.
2. **Strong level verification**: If an error message `cascading option field value does not meet the level configuration` is reported, it means that the field requires selecting the last leaf node. At this time, the `children` tree of this option is queried, and the list of last-level leaf nodes is displayed to the user for selection. Blind guessing by oneself is prohibited.

### Unwritable field types

The following field types do not support writing through the API. If encountered, they will be skipped directly and the user will be notified of the reason:

| Type | Reason |
|------|------|
| `vote-boolean` (lightweight expression) | Counter, can only be operated by the user in the interface |
| `vote-option` / `vote-option-multi` (voting) | Forging voting results through the interface is not supported |
| Calculated field | Automatically calculated by the system, read-only |

### Rich text and related fields

- **Rich Text/Multiline Text**: Directly pass Markdown strings (`# title`, `|Column 1|Column 2|`, code blocks, etc.).
- **Associated Cloud Document (PRD)**: Pass URL array, such as `["https://xxx.feishu.cn/docx/xxx"]`.
- **Pre-Dependency/Associated Work Item**: Pass the target work item ID. **Note**: Different spaces may require string `"<work_item_id>"` or numeric format. If type verification fails, switch the format immediately and try again. When the user provides a work item ** name instead of an ID **, follow the [field-value-extras.md](field-value-extras.md) "Associated Work Item Name → ID Conversion" complete process.
- **External signal type (`signal`)**: Pass `option_id` as a pure string (such as `"<option_id>"`; subject to `options[].option_id` of `workitem meta-fields`). **Does not accept `"true"` / `"false"` / `"null"`**. Currently, there are four common options for signals outside the system: `passed` / `notpassed` / `processing` / `noinformationyet`.

---

## Automatic error recovery (self-healing mechanism)

> For general self-healing rules (format error, cascading level, illegal enumeration), see [error-handling.md](error-handling.md). The following are supplementary rules to this SOP:

| Error reporting characteristics | Self-healing actions |
|---------|---------|
| `json: unsupported type` / Network timeout | Retry directly with original parameters |
| Field key does not match | Use `field_query` fuzzy search to find the best match |
| Name resolution failed | Try searching again using email prefix |
| Required fields are clearly missing | Check field type restrictions, try number ↔ string switching to associate work items |

---

## Circuit Breaker

> For general circuit breaker rules (space not found, insufficient permissions), see the main document [SKILL.md](../SKILL.md) "Error Handling". The following are supplementary rules to this SOP:

1. **Work item type not found**: `workitem meta-types` failed more than 3 times
2. **Extensive field conversion failure**: The failure rate of field value conversion is > 60%. Terminate the process and list the details of failed fields. **Do not forcibly create incomplete data**

---

## FAQ

| Problem | Solution |
|------|------|
| User has not specified space | Ask user |
| The user has not specified the type | If there is only one type of space, use it directly, otherwise ask the user |
| The field mentioned by the user does not exist | `workitem meta-fields(field_query="keyword")` Fuzzy query, inform the user if not found |
| There are multiple templates | Match based on keywords. If no match is found, a list will be displayed for the user to choose |
| The enumeration value cannot match | Display all the enumeration values ​​of this field for the user to choose |
| Person name matched to multiple people | Display the complete list for users to specify, **No self-selecting** |
