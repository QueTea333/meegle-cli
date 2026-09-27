# MQL complete example and keyword mapping

The field keys, roles, and options in the example are all returned by `workitem meta-fields` / `workitem meta-roles` (pass both `--project-key` and `--work-item-type` at the same time).

## 1. Array contains + current user + unarchived

```sql
SELECT `work_item_id`, `name`, `work_item_status`, `priority`
FROM `project_key`.`story`
WHERE array_contains(`current_status_operator`, current_login_user()) AND `archiving_date` IS NULL
```

## 2. Relative time; overdue but not completed (date range field + status filtering)

```sql
SELECT `work_item_id`, `name`, `start_time` FROM `project_key`.`story`
WHERE RELATIVE_DATETIME_BETWEEN(`start_time`, 'past', '30d')
-- Overdue and not completed
SELECT `work_item_id`, `name`, `work_item_status` FROM `project_key`.`story`
WHERE RELATIVE_DATETIME_LT(`__field_xxxxxx_end time`, 'today') AND `work_item_status` != '<Complete status label>'
```

## 3. Team role (`team list` prefixed)

`'Real team name'` is a placeholder, you must first use `team list --project-key` to get the real team name, otherwise `attribute_value not found` will be reported.

```sql
SELECT `work_item_id`, `name`, `priority` FROM `project_key`.`story`
WHERE any_match(`__Backend Development`, x -> x IN (team(true, 'real team name')))
-- fallback (only when character names conflict)
WHERE any_match(`__role_<project_key>_story_<role_id>`, x -> x IN (team(true, 'real team name')))
```

## 4. Comprehensive (fuzzy + array + sorting and truncation; use `--session-id` to turn pages in full)

```sql
SELECT `work_item_id`, `name`, `work_item_status`, `priority` FROM `project_key`.`issue`
WHERE `name` LIKE '%Performance Optimization%' AND array_contains(`current_status_operator`, current_login_user()) AND `priority` = 'P0'
ORDER BY `updated_at` DESC LIMIT 50
```

## 5. Node attributes + extension identifier

```sql
-- The current person in charge of the node to which it belongs (single condition; `__BELONGING` attribute prohibits AND combination, see mql-nodes-relations.md §4)
SELECT `work_item_id`, `name`, `work_item_status` FROM `project_key`.`story`
WHERE array_contains(get_node_attribute('__BELONGING','Current person in charge'), '<id:userkey>')
--Owning node status: must = top-level comparison, array_contains package is prohibited
SELECT `work_item_id`, `name` FROM `project_key`.`story`
WHERE get_node_attribute('__BELONGING','status') = '<in progress status label>'
-- The start node has been extended (collections are exactly equal, except for JSON array strings)
SELECT `work_item_id`, `name` FROM `project_key`.`story` WHERE risk_label() = '["Delay/Start"]'
```

## 6. Relational query (chain + cross-spatial field)

```sql
-- The subtask's parent work item name contains "Login"
SELECT `work_item_id`, `name` FROM `project_key`.`sub_task`
WHERE any_relation_match(relation_field_chain('__parent work item'), x -> x.`name<target:all>` like '%login%')
-- Multi-level relationship: subtask → parent work item → associated software
SELECT `work_item_id`, `name` FROM `project_key`.`sub_task`
WHERE any_relation_match(relation_field_chain('__parent work item','requirement related software'), x -> x.`name<target:all>` = 'some software')
```

## 7. Status time + node leader

```sql
-- issue (status flow): status window
SELECT `work_item_id`, `name`, `work_item_status` FROM `project_key`.`issue`
WHERE status_time('<status name>') between '2025-01-01' and '2025-12-31'
-- story (node ​​flow): start node leader
SELECT `work_item_id`, `name` FROM `project_key`.`story`
WHERE array_contains(get_node_attribute('start','person in charge'), '<id:userkey>')
```

## 8. Keyword → Grammar mapping

| User keywords | Grammar |
|-----------|------|
| Participants, all participants | `all_participate_persons()` |
| Current participants | `participate_persons()` |
| Process nodes, all nodes | `all_nodes_name()` |
| Nodes in progress | `in_progress_nodes_name()` |
| Node scheduling, node evaluation, and belonging nodes | `get_node_attribute(node, attr)` (use `__BELONGING` for the belonging node) |
| Node deferred label | `risk_label()` |
| Relation work item fields | `relation_field_chain('rel1',...)` (≤ 3 hops) |
| Subtask parent work item | `relation_field_chain('__parent work item')` |
| Subtask source | `linked_work_item()` |
| Status time window (status flow) | `status_time('status name') between ...` |
| Status cumulative duration (status flow) | `status_time('__<status name>_end time') - status_time('__<status name>_start time')` (SELECT only) |
| Relational context "every"/"there is one"/"every dissatisfaction"/"there is one dissatisfaction" | `all_relation_match` / `any_relation_match` / `none_relation_match` / `not all_relation_match` |
| Array semantics | Syntax |
|------|------|
| Existing options belong to (OR) | `IN + tuple` (preferred); use `any_match` for control functions |
| None of the options | `NOT IN` (preferred); `none_match` alternative |
| Contains both (AND) | `array_contains(field, 'a', 'b')` |
| Sets are exactly equal | `` `field` = ('v1','v2') `` (JSON array strings are prohibited, except for control functions) |
