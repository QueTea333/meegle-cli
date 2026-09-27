# MQL node functions and relationship functions

Catalog: 1 Node functions · 2 `get_node_attribute` · 3 Control function multi-value semantics · 4 `__BELONGING` Server-side restrictions · 5 Relationship judgment functions · 6 Relationship parameters and cross-end fields · 7 Other relationship functions

## 1. Node function

| Function | Purpose |
|------|------|
| `all_nodes_name()` | Array of all node names |
| `in_progress_nodes_name()` | Array of in-progress node names |
| `risk_label()` | Array of node extension status identifiers, `any_match` is not supported |
| `get_node_attribute(node, attribute)` | Specify node attributes; `node` can be the node name / `__ALL` / `__BELONGING`; "owning node" must use `__BELONGING` |

The node name is subject to the return of `workflow get-node` (`--node-id-list '["_all"]'`, and pass `--project-key` and `--work-item-id` at the same time).

## 2. `get_node_attribute` attribute access

Available attributes: scheduling, score evaluation, node time, node completion conclusion, node completion opinion, person in charge, current person in charge, and status. When specifying the node owner, `owner` is equivalent to `responsible person`.

The second parameter form of the start/end time: node schedule `'__scheduling_start time'` / `'__scheduling_end time'`; node time `'__node time_start time'` / `'__node time_end time'`.

`__ALL` abbreviation: directly use interval/comparison for the overall attributes, prohibit splitting `__scheduling_start time` / `__scheduling_end time`, and prohibit `any_match`; `scheduling` supports `BETWEEN`, `estimate` does not support `BETWEEN` (split `>= a AND <= b`).

```sql
WHERE array_contains(get_node_attribute('Detailed Requirements','owner'), '<id:userkey>') -- Specify the node person in charge
WHERE RELATIVE_DATETIME_BETWEEN(get_node_attribute('start','__schedule_start time'), 'past','30d') -- Start node schedule in the past 30 days
WHERE array_contains(get_node_attribute('__BELONGING','Current person in charge'), '<id:userkey>') -- The current person in charge of the node to which it belongs
WHERE get_node_attribute('__ALL','Scheduling') between '2026-01-01' and '2026-03-31' -- All node schedules are within the range
WHERE get_node_attribute('__ALL','Estimated score') >= 30 -- All node evaluation scores ≥ 30
```

## 3. Control function multi-value semantics

`all_nodes_name()` / `in_progress_nodes_name()` (cannot use `IN`, can only use `any_match`):

| Semantics | Writing |
|------|------|
| Existing options belong to (OR) | `any_match(<control>, x -> x IN ('a','b'))` |
| Contains (AND) | `array_contains(<control>, 'a','b')` |
| Sets are exactly equal | `<control> = '["a","b"]'` (JSON array, exception) |
| None of them | `none_match(<control>, x -> x IN ('a','b'))` |
| Not both included (at least one is missing) | `NOT array_contains(<control>, 'a','b')` |

`risk_label()` (does not support `any_match`): There is intersection (default "including deferred nodes") `array_intersect(risk_label(), '["Deferred/front-end", "Deferred/back-end"]')`; all included with multiple `array_contains(risk_label(), 'x')` AND;
The parent "postponed"/"scheduling information is incomplete" uses `array_contains(risk_label(),'postponed')`; the start node has been postponed `risk_label() = '["postponed/start"]'` (the collections are completely equal, with the exception of JSON arrays).

## 4. `__BELONGING` Server-side restrictions (authoritative definition)

Once `get_node_attribute('__BELONGING','status')` is wrapped by `array_contains` (triggered by a single condition), or there is an AND combination between any `__BELONGING` attributes (such as "current person in charge + status"), nil pointer / panic will be triggered.
Fix: Status classes must use `=` top-level comparison; multi-valued attributes such as person in charge must remain single condition; use the `__ALL` attribute instead when combination is required, or splice the status field `work_item_status` + `current_status_operator`.

```sql
WHERE get_node_attribute('__BELONGING','status') = '<in progress status label>' -- ✅ top level =, return empty list no error
```

## 5. Relationship judgment function

Make conditional judgments on the relationship peers: `any_relation_match(rel, x -> expr)` There is one peer that satisfies; `all_relation_match` each of them satisfies; `none_relation_match` each of them does not satisfy; `not all_relation_match` at least one does not satisfy.
`relation_field_chain` must be wrapped with a relationship judgment function after obtaining the relationship.

## 6. Relationship parameters and cross-end fields

There are three forms of relationship parameters: 1) associated field `` `field key` ``; 2) `relation('relation name')`; 3) `relation_field_chain('rel1', 'rel2' [, 'rel3'])` (≤ 3 hops). The relationship name of the child task's parent work item is fixed `'__parent work item'` (double underscore prefix).

Peer field reference: `` x.`Field name <target:project_key::type_key>` `` or `` x.`Field name <target:all>` ``. Common fields must use `<target:all>`: title, creator, creation time, business line, priority, current person in charge, belonging work item, belonging space, work item ID, work item type, status.

```sql
WHERE any_relation_match(`Multi-select related fields`, x -> x.`priority<target:all>` = 'P0') -- Peer priority P0
WHERE any_relation_match(relation_field_chain('__parent work item'), x -> x.`name<target:all>` like '%login%') -- parent work item name
WHERE all_relation_match(relation_field_chain('__parent work item','requirement related software'), x -> array_contains(get_node_attribute('start','responsible person'), '<id:userkey>'))
```

## 7. Other relational functions

- `parent_work_item(relation('relation name'))`: Parent work item ID, only supports SELECT, WHERE reports `parent_work_item() not supported in stage Where`;
To filter by parent work item instead use `` any_relation_match(relation_field_chain('__parent work item'), x -> x.`work_item_id<target:all>` = '12345') ``.
- `association()`: associate instance ID across space, `WHERE association() = 'instance ID'`.
- `linked_work_item()`: subtask source control (parent work item ID). `IS NOT NULL` is recommended for null judgment; the equivalent rvalue must be the real parent work item ID, otherwise `attribute_value not found`.
