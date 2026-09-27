# Workflow auxiliary commands

An auxiliary command used to query the transferable direction, required items, and node field configuration before workflow transfer. For core flow commands (`workflow transition` / `workflow transition-state` / `workflow get-node` / `workflow update-node`), see the SKILL.md main file.

## workflow list-state-transitions
View the status list of work items that can be transferred. This command must be called to get `transition_id` before the state flow is transferred.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-id | string | yes | work item ID |
| --work-item-type | string | yes | work item type |
| --user-key | string | yes | user ID |

## workflow list-state-required
View the required information required for transfer (node ​​transfer node_key, state transfer state_key).

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-id | string | yes | work item ID |
| --state-key | string | Yes | The node_key of the node stream or the state_key of the state stream |
| --mode | string | No | Check all required fields by default; pass `unfinished` to only check unfinished required fields |

## workflow meta-node-fields
View node field configuration. `workflow update-node` is used to confirm the legal field_key, field type and options before modifying node custom fields. When querying the review conclusion options, press `field_keys=["node_finished_conclusion"]` to query accurately, and take legal values ​​from the options/option list of the returned configuration.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-type | string | yes | work item type |
| --field-keys | array | No | Exactly match node field key or name, such as `["node_finished_conclusion"]` |
| --field-types | array | No | Filter by node field type |
| --query | string | No | Fuzzy search by field name / key |
