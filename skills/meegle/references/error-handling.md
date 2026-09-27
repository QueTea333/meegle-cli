# Error handling rules

> Symptoms → Fix action mapping. Syntax/protocol details refer to `mql-basics.md` / `mql-operators.md` / `mql-time-status.md` / `mql-people-roles.md` / `mql-nodes-relations.md` and SKILL.md "field value format".

**General Principle**: Extract keywords from error report → Match the following table to fix → Try again. **The same error will be automatically retried up to 2 times (3 attempts in total)**. If it still fails, stop self-healing and provide the user with a structured explanation of: ① the original request and target semantics; ② the key changes and server returns for each repair; ③ speculate the root cause (field/role/enumeration does not exist, no permissions, etc.) and request clarification. **Infinite retries or repeated fine-tuning of the same parameters are prohibited**.

---

## 1. MQL self-healing rules (high frequency, priority matching)

> `workitem meta-fields` / `workitem meta-roles` must have both `--project-key` and `--work-item-type`; `workitem meta-types` only needs `--project-key`.

### 1.1 Metadata class

| Error symptoms | Repair actions |
|---------|---------|
| The field key does not exist in this space + work item type | Immediately call `workitem meta-fields`, and pass the space, type and field name in the error report at the same time, replace and try again |
| Field Chinese name ambiguity (multiple fields with the same name) | Use field key instead. Get all matching keys through `field_query` of `workitem meta-fields`, and select the semantically correct replacement; it is impossible to judge and ask the user |
| Write roles as fields (such as `handler`) | See [mql-people-roles.md §3 Role Column](mql-people-roles.md): `workitem meta-roles` takes `role_name` / `role_id`, use `` `__<role_name>` `` or fallback compound column name |
| The enumeration value/status value does not exist in this space + work item type | `workitem meta-fields` accurately obtains the options of the corresponding field and replaces it with the real label. No hardcoding |
| Write the tree field value as a complete path or non-leaf | `workitem meta-fields` Get options and confirm the leaf `option_id` / label; use `any_match` for parent query instead |
| Query for unsupported field types (`attachment` / `file` / `spec_doc` / `specDocs`) | Removed from SELECT/WHERE, use `workitem get` instead. **`multi-file` exception**: `SELECT` / `IS NULL` / `IS NOT NULL` allowed; deep filtering not supported |

### 1.2 Grammar class

| Error symptoms | Repair actions |
|---------|---------|
| Chinese character encoding is damaged | Press [mql-basics.md §10](mql-basics.md) to retain the original MQL text; Shell calls heredoc with quote delimiters to avoid quotes and backtick expansion. Refactor retry |
| Used hard rule disabling syntax (`SELECT *`, `count()`/`GROUP BY`, `REGEXP`, `CONTAINS()`) | Compare [mql-basics.md §3](mql-basics.md). `LIMIT n` only truncates (n ≤ 50), do not use `LIMIT offset, n` to turn pages, use `session_id` + `group_pagination_list` |
| The field name does not include backticks, resulting in `syntax error near ':...'` | The field name uses backticks; when it contains `<target:xxx>`, put the same pair of backticks as a whole (`` `name<target:all>` ``) |
| Top-level SELECT/WHERE uses `` `name<target:all>` `` to report `attribute[...] not found` | `<target:xxx>` **Only** allowed in relationship judgment lambda (`` x.`name<target:all>` `` form); top-level reference removes modifier |
| Escape single quotes with `\''` | Use `''` (two single quotes) instead |
| `Internal % and _ characters must be escaped` | LIKE internal literals `%` / `_` must be written as `\%` / `\_`; even if `_` appears in the middle of keywords (such as `%test_case%`), it will be forcibly rejected by the server and must be escaped to `%test\_case%`. See [mql-basics.md §7](mql-basics.md) |
| LIKE missing wildcard | Complete inclusion form `LIKE '%keyword%'` |

### 1.3 Semantic class

| Error symptoms | Repair actions |
|---------|---------|
| FROM missing space or type | Corrected to `` FROM `project_key`.`work_item_type_key` `` |
| Functions (`current_login_user()`, `array_contains()`) were placed in SELECT | Removed from SELECT, functions can only be in WHERE |
| `parent_work_item() not supported in stage Where` | `parent_work_item()` only supports SELECT; in WHERE, filter by parent work item instead use `` any_relation_match(relation_field_chain('__parent work item'), x -> x.`work_item_id<target:all>` = '<parent ID>') `` |
| The operator is incompatible with the field type | Corrected by comparing [mql-operators.md §1 Compatibility Table](mql-operators.md). Common: `number` / `bigint` does not support `BETWEEN` / `LIKE` / `array_contains`, changed to `>=` + `<=` / exact equivalent |
| Multi-valued rvalue uses JSON array string (`IN '["a","b"]'`) | `IN` direct syntax error, change tuple `IN ('a','b')`. `=` The server is currently compatible with JSON array strings but it is not a recommended writing method. Change the tuple `= ('a','b')` uniformly. **Exception**: `array_intersect` / `risk_label() = ...` retain JSON array string |
| `tree-multi-select` / `workitem_related_multi_select` rvalue rejects `metadata error` or `attrValueLabel not found` (such as bare option_id / bare work_item_id) | Change to label or `<id:option_id>` / `<id:work_item_id>` package form. See [mql-operators.md §1](mql-operators.md) |
| signal field `IS NULL` / `IS NOT NULL` reports `operator not supported` | signal does not support null judgment, use `!= 'real label'` instead or remove this condition |
| The signal value is passed `option_id` / `<id:>` / `'true'` / `'false'` / `'null'` | Use `option_name` label instead (such as `'passed'`) |
| Lambda reports `lambda predicate operator not supported: <OP>` / `unsupported lambda predicate` | Lambda only supports `x = 'v'`, `x IN (...)`, and the same variable OR. The rest (`!=` / `NOT IN` / comparison operator / `LIKE` / `BETWEEN` / `IS NULL` / `RELATIVE_DATETIME_*` / `AND` compound / nested Match) are all rejected. See [mql-operators.md §5](mql-operators.md). For multi-select/array fields, use the top-level `IN` / `NOT IN` / `array_contains` / `none_match`; only use `any_match` when the value list contains `team()` / `current_login_user()` |
| `get_node_attribute('__BELONGING','status')` triggers nil pointer / panic | See [mql-nodes-relations.md §4](mql-nodes-relations.md) Authoritative rules: state classes use `=` top-level comparison instead (not allowed to be wrapped by `array_contains`); `__BELONGING` attributes are forbidden to be combined with AND |

### 1.4 Parameter class

| Error symptoms | Repair actions |
|---------|---------|
| `RELATIVE_DATETIME_*` reports `unexpected operator for future` / `invalid argument` | See [mql-time-status.md §1.1 Compatibility Matrix](mql-time-status.md): `_EQ` only accepts `today`/`tomorrow`/`yesterday` and **no offset**; `_GT/_GE/_LT/_LE` only accepts `today` (can bring `±Nd`); `future`/`past`+`Nd` only allows `_BETWEEN` |
| ORDER BY field does not support sorting | Replace sortable fields (such as `updated_at` / `start_time` / `work_item_id`) or remove |
| Both MQL and session_id are empty | MQL must be passed for the first query; session_id returned by the first query must be passed for page turning |
| Page turning parameters are missing / page turning still returns to page 1 | Pass `--group-pagination-list '[{"group_id":"1","page_num":N}]'` (when there is no grouping, `group_id` is fixed to `"1"`, N starts from 2); do not pass `--mql` when turning pages, the simultaneous interpretation will rerun according to the new query |
| project_key is not passed | `--project-key` of `workitem query` is required |
| Space does not exist | Confirm with `project search` |
| Multiple space names match | Extract candidates from the error report and let the user select or try again with the exact project_key |
| The current user does not have permission to this space | Inform the user that he or she needs to apply for access and **retry is prohibited** |

---

## 2. General field value self-healing

| Error symptoms | Repair actions |
|---------|---------|
| `need STRING type, but got: LIST/MAP` | Native JSON changed to JSON.stringify string (see SKILL.md "Field value format") |
| Field value type mismatch (number ↔ string, single value ↔ array) | Only the format is changed, the value remains unchanged |
| Cascading options to pass non-leaf nodes | Display the `children` tree and let the user select leaves |
| The enumeration value is not in the optional items | Match from options; if the only hit is correct, retry, otherwise ask the user |

---

## 3. Non-MQL error quick check

| Error symptoms | Repair actions |
|---------|---------|
| Space not found / Chinese name multiple hits | `project search` verification, take project_key and call it accurately |
| Work item type not found | `workitem meta-types` confirmed to be legal `type_key` |
| Template does not exist/disabled | `workitem meta-fields` Get available templates |
| No results for role query / Manager reporter cannot be found | `workitem meta-roles` Confirm `role_name` / `role_id`; system default `role_name`=`Manager`/`Reporter`, `role_id`=`operator`/`reporter` |
| Person name→userkey conversion failed | `user search` batch query |
| Failed to write the personnel field | user passes a single userkey; multi-user must be a stringified array (such as `"[\"k1\",\"k2\"]"`) |
| Node not found | `workflow get-node` checks the full node details list to match the real `node_id` (`workitem get` only returns nodes in progress) |
| Node transfer failed | Node flow uses `workflow transition`; state flow uses `workflow transition-state` (first `workflow list-state-transitions` takes `transition_id`, `workflow list-state-required` is required) |
| Repeat the transfer of completed nodes | Check the status with `workflow get-node` before transfer |
| The node is not activated | Confirm the workflow progress first |
| Create missing template | `workitem meta-fields` Get template fields |
| Create required fields not provided | `workitem meta-create-fields` takes all fields with `is_required=1` (note that `workitem meta-fields` does not return the required tag) |
| Role update failed | Use `role_operate` of `workitem update` instead (without fields) |
| `group_type=bind` is missing `group_id` | Fill in non-empty `group_id`; use `{"type":"disabled"}` to unbind |
| `group_type=auto/disabled` but passed `group_id` | Choose one: keep `bind + group_id`, or remove `group_id` |
| `page_size` is serialized to a string | Meegle CLI uses `--params '{"page_size":N,"page_token":"..."}'` instead |
| `mywork todo` needs to select the workspace | Pass `asset_key` (Asset_xxx) from the error list and try again |
| Illegal operation recording time interval | `start_time < end_time`, format millisecond timestamp or `YYYY-MM-DD` |
| Relation query is missing `relation_id` | First use `relation meta-definitions` to obtain |
| Interface current limit | Wait 1-2 seconds to try again, or reduce concurrency |
| View configuration invalid/session cache expired | The former `view search` takes a valid ID; the latter does not pass `session_id` and rechecks |
| The work item has been frozen/terminated/archived | Inform the user that it is not editable and **retries are prohibited** |
| The state transition target is unreachable | First `workflow list-state-transitions` is used to obtain legal paths for users to choose |
| Option visibility is not satisfied | Reuse `workitem meta-fields` to get contextually visible options |
| `work_item_type_key` does not exist | `workitem meta-types` takes the legal list |
| WBS draft does not exist | Use `wbs create-draft` first, or confirm that the space has WBS enabled |
| Non-IPD projects call WBS/repository/deliverables | Inform users that the space is not supported |
| `wbs edit-draft` The parent row does not support adding new child rows | `wbs list-draft-rows` Confirm the parent row type and disassembly mode |
| Work time is not enabled | It needs to be turned on in the project settings (`actual_work_time_switch=true` or install the "work time registration" plug-in), **retry is prohibited** |
| The work record returns empty but it is confirmed that there is data | Confirm whether `work_item_type` is correct |

---

## 4. Circuit breaker condition (immediate termination)

- Space not found (`project search` failed 3 times in a row)
- Permission Denied (no space access permission)
- The server returns a clear "Archived/frozen/no permission" business error
