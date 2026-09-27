# MQL time function, date interval field and status function

Directory: 1 `RELATIVE_DATETIME_*` · 2 Date range field (date_range) · 3 `status_time`

## 1. Time function `RELATIVE_DATETIME_*`

Signature: `RELATIVE_DATETIME_{EQ|GT|GE|LT|LE|BETWEEN}(col_name, 'date_para', ['days'])`

date_para：`today` / `tomorrow` / `yesterday` / `current_week` / `next_week` / `last_week` / `current_month` / `next_month` / `last_month` / `future` / `past`。

### 1.1 Function × date_para × days compatibility matrix

Server-side rejection of `unexpected operator for <para>` / `invalid argument` on combinations that do not allow columns.

| Function | Recommended date_para | days (`'Nd'` / `'-Nd'`) | Remarks |
|------|----------------|--------------------------|------|
| `_EQ` | `today` / `tomorrow` / `yesterday` | ❌ Not accepted | `_EQ + future/past` without offset reports `invalid argument: relative datetime <para> expr must have 2 params`; with offset, the semantics are ambiguous and generation is prohibited, use `_BETWEEN` instead |
| `_GT` / `_GE` / `_LT` / `_LE` | `today` only | ✅ Can be positive or negative | — |
| `_BETWEEN` | `current_week` / `next_week` / `last_week` / `current_month` / `next_month` / `last_month` / `future` / `past` | Only `future` / `past` is accepted and must pass `'Nd'`; others are not accepted | Reject `today` / `tomorrow` / `yesterday` (`unexpected operator for today`), use `_EQ` instead |

### 1.2 Example

```sql
RELATIVE_DATETIME_EQ(`start_time`, 'today') -- created today
RELATIVE_DATETIME_BETWEEN(`start_time`, 'last_week') -- Created last week
RELATIVE_DATETIME_BETWEEN(`field_xxxxxx`, 'future', '3d') -- Expires in the next 3 days
RELATIVE_DATETIME_GT(`start_time`, 'today', '-3d') -- 3 days forward from today
RELATIVE_DATETIME_LT(`start_time`, 'today', '3d') -- 3 days ago today
```

The field key must be confirmed by `workitem meta-fields` first (pass `--project-key` and `--work-item-type` at the same time), and it is forbidden to reuse the example key.

## 2. Date range field (date_range)

Work item-level date interval fields (customized "planning period", etc., field type `schedule` / `precise_date`) cannot be queried directly with the field key and must be split into a derived access form: `` `__<field key>_start time` `` / `` `__<field key>_end time` ``. This is not a composite field subfield concept.

```sql
✅ WHERE `__field_xxxxxx_start time` > '2025-01-01'
✅ WHERE RELATIVE_DATETIME_BETWEEN(`__field_xxxxxx_endtime`, 'past', '30d')
❌ WHERE RELATIVE_DATETIME_BETWEEN(`field_xxxxxx`, 'past', '30d')
```

Node scheduling is different from this: accessed with `get_node_attribute(node, '__scheduling_starttime')`, see `mql-nodes-relations.md`.

## 3. Status function `status_time`

Valid for both status flow (such as `issue`) and node flow (such as `story`) work items. If the status name does not exist, a `metadata error` will be reported;
The state name is based on the real option returned by `workflow list-state-transitions` or `workitem meta-fields --field-keys '["work_item_status"]''.

| Usage | Allowed positions | Parameter forms |
|------|---------|---------|
| `status_time('status name')` | WHERE / SELECT | Pure status name without `__` prefix or `_start time` / `_end time` suffix |
| `status_time('__status name_start time')` / `_end time` | Only SELECT or time difference expressions | An error will be reported when used in WHERE |

```sql
-- ✅ WHERE filter with pure status name (date range)
WHERE status_time('<status name>') between '2025-01-01' and '2025-12-31'
-- ✅ SELECT calculates the cumulative status duration
SELECT `work_item_id`, status_time('__<status name>_end time') - status_time('__<status name>_start time') FROM `project_key`.`issue`
-- ❌ Do arithmetic filtering in WHERE
WHERE status_time('__<status name>_end time') - status_time('__<status name>_start time') > 86400
```
