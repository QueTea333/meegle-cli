# MQL operator compatibility, array functions and Lambda

Contents: 1 Operator × Field type compatibility table · 2 Array field semantics and tree matching · 3 Array and aggregate functions · 4 Multi-select field decision order · 5 Lambda restrictions

## 1. Operator × field type compatibility table (unique authoritative table)

Violation of this form will report `field not supported OPERATOR` / `operator not supported`.

| Field type | Supported | Not supported |
|---------|------|--------|
| text / multi-pure-text | `=` `!=` `IN` `NOT IN` `LIKE` `NOT LIKE` `IS NULL` `IS NOT NULL` | Comparator, `BETWEEN`, `array_contains` |
| multi-text | `LIKE` `NOT LIKE` `IS NULL` `IS NOT NULL` | Others |
| select / radio | `=` `!=` `IN` `NOT IN` `IS NULL` `IS NOT NULL` | `LIKE`, comparison operator, `BETWEEN`, `array_contains`, `any_match` |
| workitem_related_select / workitem_related_multi_select | `=` `!=` `IN` `NOT IN` `IS NULL` `IS NOT NULL`, the rvalue must be the **exact full name** of the peer** or `'<id:work_item_id>'' | `LIKE`, comparison operator, `array_contains`, `any_match`; when the user gives a short name or a fuzzy name, **do not use equivalent values**, change `any_relation_match`, see [mql-nodes-relations.md §5](mql-nodes-relations.md) |
| tree-select (single selection) | `=` `!=` `IN` `NOT IN` `IS NULL` `IS NOT NULL` (only leaf exact match) | `LIKE`, comparison operator, `BETWEEN`, `array_contains`, `any_match`, parent cascade |
| user | `=` `!=` `IN` `NOT IN` `array_contains` `IS NULL` `IS NOT NULL` | `LIKE`, comparison operator, `BETWEEN` |
| link | `=` `!=` `LIKE` `NOT LIKE` `IS NULL` `IS NOT NULL` | `IN`, comparison operator, `BETWEEN`, `array_contains` |
| signal | `=` `!=` `IN` `NOT IN` (see below for value bits) | `IS NULL` / `IS NOT NULL` (report `operator not supported`), others |
| number (including `work_item_id`) | `=` `!=` `IN` `NOT IN` `>` `>=` `<` `<=` `IS NULL` `IS NOT NULL` | `LIKE`, `BETWEEN`, `array_contains` |
| array(varchar) | `=` `!=` `IN` `NOT IN` `array_contains` `NOT array_contains` `any_match` `none_match` `IS NULL` `IS NOT NULL` | `LIKE`, comparator, `BETWEEN` |
| date | `=` `!=` `>` `>=` `<` `<=` `BETWEEN` `RELATIVE_DATETIME_*` `IS NULL` `IS NOT NULL` | `LIKE`、`IN`、`array_contains` |
| datetime（`schedule` / `precise_date`) | `>` `>=` `<` `<=` `BETWEEN` `RELATIVE_DATETIME_*` `IS NULL` `IS NOT NULL`, must be split into `` `__field key_start time` `` / `` `__field key_end time` `` | `=` `!=`, `LIKE`, `IN`, `array_contains` |
| bool | `=` `!=` `IS NULL` `IS NOT NULL` | `IN`, `LIKE`, `BETWEEN`, `array_contains`, comparison operators |
| array(struct)(`compound_field`) | Cannot be compared directly in WHERE | Need to be read through subfield or `workitem get` |
| `multi-file` | `SELECT` / `IS NULL` / `IS NOT NULL` | `LIKE`, comparison operator, `BETWEEN`, `array_contains`, `IN` |

Value bit supplement:

- The signal query value bit only accepts `option_name` label (`'passed' / `'not passed'` / `'processing'` / `'no information yet'`); prohibits `option_id` (`'passed``), `<id:option_id>`, `'true'/'false'/'null'`.
The value bit rules for writing to the interface are opposite (write `option_id`), and do not copy them on the query side.
- `workitem_related_select` value bit: `= '<id:work_item_id>'` or the **exact full name** of the peer; bare `work_item_id` rejects `attrValueLabel not found`.
When the user only provides an abbreviation or a name fragment, the equivalent value must be reported as `attrValueLabel not found`. Do not guess the name again and rewrite it as a relationship judgment:

  ```sql
WHERE any_relation_match(`<relation field key>`, x -> x.`name<target:all>` LIKE '%<name fragment>%')
  ```

The first parameter is the associated field key returned by `workitem meta-fields`; for the peer field reference form (the respective applicable scopes of `<target:all>` and `<target:project_key::type_key>`), see [mql-nodes-relations.md §5–6](mql-nodes-relations.md).
- `tree-multi-select` / `workitem_related_multi_select` rvalue only accepts label or `<id:option_id>` / `<id:work_item_id>` wrapped form; bare id rejects `metadata error`.

The associated work item fields preferentially follow the special restrictions in the above table, and the general array function rules below do not apply.

## 2. Array field semantics and tree matching

- `=` / `!=` matches the entire group exactly; `IN` matches element-wise OR; `array_contains` multi-parameters are AND (contained simultaneously); `any_match` matches element-by-element.
- Tree/cascade field parent matching (including all subordinates): `tree-multi-select` (array type) uses `any_match(field, x -> x = '<parent label>')` or `` `field` IN ('<parent label>') ``;
`array_contains` Only exact label matches, no child cascades. `tree-select` (single selection) does not support parent cascading, only exact matching by leaf label / option_id. It is forbidden to write the full path `'<parent>/<leaf>'`.
- For tree fields such as business line, first use `workitem meta-fields --field-keys '["business"]'` (or `--field-query business line`) to confirm the leaf option_id and label.

## 3. Array and set functions

| Function | Description |
|------|------|
| `array_contains(field, e1 [,e2,...])` | Array contains elements; multiple parameters are AND. The rvalue form is supplemented by §1 value bits |
| `any_match(field, x -> pred)` | Any element is satisfied; only used when the value list contains a function (`team()` / `current_login_user()`) or multiple `<id:userkey>` |
| `none_match(field, x -> pred)` | All elements are not satisfied |
| `array_intersect(field, '[...]')` | There is intersection; only used for control function return values ​​(`risk_label()`, etc.), ordinary multi-select fields report `array_intersect: invalid right_array`; the second parameter must be a JSON array string |

## 4. Multi-select/array field decision order

1. Single element or multi-element OR → `IN + tuple`
2. Multi-element AND (contains simultaneously) → `array_contains(field, 'a', 'b')`
3. The sets are completely equal → `` `field` = ('v1','v2') `` (tuple, JSON array string)
4. None → `NOT IN` or `none_match`; `NOT array_contains` is "not included at the same time" (at least one is missing), not "not at all"
5. Tree parent matching → see §2
6. The value list contains `team()` / `current_login_user()`, or multiple `<id:userkey>` → `any_match(x -> x IN (...))` must be judged in the set

```sql
array_contains(`current_status_operator`, '<id:userkey>') -- single hit (multiplayer field)
`current_status_operator` IN ('<id:k1>', '<id:k2>') -- multi-person OR
array_contains(`tag`, 'Tag A', 'Tag B') -- Multiple tags AND
`tag` IS NULL -- null judgment
any_match(`watchers`, x -> x IN (team(true, 'real team name'))) -- list of values ​​containing team functions
```

## 5. Lambda limitations

The internal server of the `x -> ...` of the array matching function `any_match` / `none_match` only accepts the following minimal conditions, and rejects the rest as `lambda predicate operator not supported: <OP>` / `unsupported lambda predicate`.

| ✅Supported | ❌ Not supported |
|--------|----------|
| `x = 'value'` | `x != 'a'`、`x NOT IN (...)` |
| `x IN ('a','b',...)` (value can contain `team(...)` / `current_login_user()` / `<id:userkey>`) | `> >= < <=`, `LIKE`, `BETWEEN`, `IS NULL` |
| Same variable OR: `x = 'a' OR x = 'b'` (recommended to change to `IN`) | `RELATIVE_DATETIME_*(x,...)`, `AND` compound, nested `any_match` / `none_match` |

Decision: multi-select / array fields first top-level `IN` / `NOT IN` / `array_contains` / `none_match`; `any_match` second argument must be `x -> ...` lambda, array literals are not accepted.
