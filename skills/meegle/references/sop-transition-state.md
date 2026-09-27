# Transfer work item status (status flow)

> **CRITICAL** — Before starting, MUST use the Read tool to read `../SKILL.md`, which contains pre-checking, authorization process, command parameter reference, field value format, general specifications and error handling.

This skill is used for the status flow of **status flow work items** (such as defects/issues), and the entire process is automatically orchestrated.

> ⚠️ **State flow only**. Node flow work items such as requirements/story must use `workflow transition` (action=confirm/rollback) instead. Do not mix this Skill.

---

## Execution process

### STEP 1 — Locate work item + get current user (parallel)

**Parallel execution**:

1. **Locate work items**: Extract `work_item_id`, `project_key`, `work_item_type` from user input.
- **URL parsing**: When the user provides a link, `url decode` will be called first. Only `url_kind == workitem_detail` can enter this SOP; for other kinds, please press [url-kinds.md](url-kinds.md) to refuse or ask. The `simple_name` returned by decode must be converted into the authoritative `project_key` by `project search` (there may be multiple spaces with the same name without permission). **It is forbidden** to intercept the path segment from the URL as a parameter.
- **ID type**: `work_item_id` passed to any tool must be **String**.
- Ask questions only if there is insufficient information.
2. **Get the current user**: Call `user me` and get `user_key` from the return body (required in the next step). `current_login_user()` is only an MQL built-in function literal, `user search` will not parse this string.

### STEP 2 — Query the transferable status and match the target

```bash
meegle workflow list-state-transitions --work-item-id work item ID --work-item-type type key --user-key current user userkey --project-key space key --format json
```

- **Match target status**: Precise/fuzzy/semantic matching of user intent (such as "closed" → "closed", "resolved" → "resolved") to get the corresponding `transition_id`.
- 🚨 **When the target state is not clear and there are multiple candidates**: **All options must be displayed for the user to choose** and must not be selected by default for the user.

### STEP 3 — Fail-fast try to transfer directly

**Do not prefix required fields in the query**, call `workflow transition-state` directly:

```bash
meegle workflow transition-state --work-item-id work item ID --project-key space key --transition-id ID obtained in the previous step --format json
```

- **Transfer successful** → Jump to STEP 6 to return the result.
- **Failed and prompted that required fields are not filled in** → Enter STEP 4 to supplement as needed.
- **Other errors on failure** → Please refer to the "Smart Repair" chapter below.

### STEP 4 — Collect and supplement required fields on demand (triggered only when transfer fails)

Call `workflow list-state-required` to obtain the required fields for the target state:

```bash
meegle workflow list-state-required --work-item-id work item ID --state-key target state key --project-key space key --mode {{mode}} --format json
```

> If the tool supports the `mode` parameter (such as `mode="unfinished"`), it will first check only the fields that have not been filled in to reduce noise.

**4.1 Hard interception: Field types updated by API are not supported**

When the following fields are set as required, **immediately interrupt the flow** and prompt the user to fill them in manually on the page:

- `vote-boolean` / `vote-option` / `vote-option-multi` (voting class)
- Calculated fields (automatically calculated by the system, read-only)

> **The compound detail table can be supplemented through `workitem update` first**, but the writing protocols of `compound_field` and `multi_user_compound_field` are different; multi-person compound fields are still covered as a whole, and all personnel and non-target data must be read and retained first. For details on the format, please refer to the main document [SKILL.md](../SKILL.md) "Field Value Format → Composite Detailed List" chapter; when the subfield value or personnel range cannot be automatically determined, the user still needs to be asked.

> Example of interruption technique: "Transfer failed. The current status requires filling in [Field Name]. This field does not support automatic supplementation. Please fill it in manually on the page and notify me to continue."

**4.2 Pre-query for enumeration options (batch + precision)**

When the missing item contains an enumeration class (select/radio/multi-select/tree-select, etc.):

- Pass all target `field_key` arrays **at once** into `field_keys` of `workitem meta-fields` for precise query, and get `option_name` and `option_id`. **Never traverse the entire configuration page by page. **
- Summarize all required fields and optional values ​​** into one message ** to display and ask the user.

```bash
meegle workitem meta-fields --page-num 1 --project-key space key --work-item-type type key --field-types '{{field_types}}' --field-keys '["key1","key2"]' --field-query '{{field_query}}' --format json
```

**4.3 Field Mock and query boundary (safety bottom line)**

- **Business decision-making, personnel, date, and enumeration fields** → **AI is prohibited from fabricating or mocking data**. Must be listed and asked to the user.
- **Personnel field (user/multi-user)** → When multiple names with the same name are found or the user is not specified, confirmation must be made. **Do not fill in the current operator by default** (unless the user explicitly says "assign to me"/"I will handle it").
- **Purely descriptive text such as the reason for callback/closing** (such as "Reopen reason", "Transfer description") can be filled in by default with `"Reopen processing"` or `"Initiate transfer"` when the user does not provide it and the field semantics are not critical.

**4.4 Field value format**

After getting the user confirmation value, convert it to `field_value` according to the "Field Value Format" chapter of the main document [SKILL.md](../SKILL.md).

> 🚨 **Key Convention**: `field_value` protocol layer is **STRING**. The scalar is passed directly to the string; the array/object must be JSON.stringify**, otherwise it will report `need STRING type, but got: LIST`.

| Field type | Differentiated prompts for status transfer scenarios |
|---------|-----------------|
| `select` / `radio` / `tree-select` | **Pure string `option_id`** (🚨 Do not pass value/label JSON) |
| `multi-select` | **stringified** `"[{\"option_id\":\"xxx\"}]"`; If the field configuration allows new options and the user explicitly proposes a new value, an 8-digit random lowercase and underlined option_id can be generated to fill in |
| `tree-multi-select` | **stringified string one-dimensional array** `"[\"id1\",\"id2\"]"` (🚨 non-objectable array) |
| `signal` | option_id string (based on `options[].option_id` of `workitem meta-fields`; `"true"`/`"false"`/`"null"` is not accepted) |
| `multi-file` | Press [attachment.md](attachment.md) to execute the complete protocol: real-time disambiguation of target fields, completion of object storage upload, writing of fields and reading back the same field for acceptance; multiple attachments are appended one by one |
| `file` | is treated according to the compatibility rules of [attachment.md](attachment.md) and must not be assumed to have the same multi-attachment capabilities as `multi-file` |

> For details on the writing format of other common field types (text / number / bool / user / multi-user / date / schedule / precise_date / multi-text / workitem_related_select, etc.), please refer to the "Field Value Format" chapter of the main document [SKILL.md](../SKILL.md).

> **When the user provides a work item name instead of an ID**, it is processed according to the complete process of "Associated work item name → ID conversion" in the main document [SKILL.md](../SKILL.md) (get target constraints → `workitem query` search → disambiguation → write by type).

### STEP 5 — Complete the fields and circulate again

After all required fields are written through `workitem update`, call `workflow transition-state` again to trigger the flow:

```bash
meegle workitem update --work-item-id work item ID --project-key space key --role-operate '{{role_operate}}' --fields '[{"field_key":"xxx","field_value":"yyy"}]' --format json
```

```bash
meegle workflow transition-state --work-item-id work item ID --project-key space key --transition-id transition_id --format json
```

### STEP 6 — Return results

Show users:

- Status change direction (**from XX → YY**)
- Summary of required fields for automatic/assisted filling
- Work item ID and (if returned included) link

---

## Intelligent repair (self-healing mechanism)

> For general self-healing rules (format error, cascading level, illegal enumeration), see [error-handling.md](error-handling.md). There are no additional supplementary rules for this Skill.

---

## Circuit breaker and termination

> For general circuit breaker rules (space not found, insufficient permissions), see the main document [SKILL.md](../SKILL.md) "Error Handling". The following are supplementary rules for this Skill:

1. **Required fields are all of hard interception type** (voting/calculating fields) and cannot be written through the interface.
2. **Same target status**: Supplementary field → Failed to transfer again **> 2 times**.

---

## FAQ

| Problem | Solution |
|------|------|
| The user just said "close this bug" without giving any space | If the URL can be resolved, use the URL; otherwise, confirm with the user |
| The transferable status is empty | It means that the current status has no legal next step, and informs the user to check the status flow configuration on the page |
| The user says "Change to XX" but XX is not in the transferable list | Display the current transferable status list and let the user choose again |
| The work item is actually a node flow | Inform the user to go through `workflow transition` (action=confirm/rollback), this Skill only handles the status flow |
| Multiple options for fields with the same name | Show all option_name to allow users to select, **Prohibit taking the first item by default** |
| Multiple people with the same name | The display list allows users to specify, **It is prohibited to fill in the current operator by default** |
