#WBS schedule

The schedule (Work Breakdown Structure) has two sets of data models:

- **Draft** — An editable copy of the current user's copy. `wbs create-draft` creates, `wbs edit-draft` atomic edit (single line or batch operation), `wbs publish-draft` publishes, `wbs reset-draft` discards changes.
- **instance** — published online version. Read-only, use `wbs list-instance-rows` to query.

**Common process**: `wbs create-draft` → `wbs edit-draft` → `wbs publish-draft` multiple times.

**Auxiliary commands** (Create Draft/Reset/Progress/Template) see [misc.md](misc.md).

---

## Shared query capabilities (draft / instance)

The parameters of `wbs list-draft-rows` and `wbs list-instance-rows` are exactly the same, only the query data set is different - the former checks the current user's drafts, and the latter checks the online instances. **Mixed use is prohibited**: For example, first use `wbs list-draft-rows` to get the row uuid, and you must continue to use the draft series of commands for subsequent recursion/editing.

### condition_query support fields

Filter rows using `condition_query` (object). **Only the following fields are supported**:

| Field | Meaning | Value |
|------|------|------|
| `wbs_name` | Row name / task name / schedule item name / sub-item name | string |
| `wbs_parent_id` | Parent uuid (used to check children/subordinates/subtasks/child items) | uuid |
| `wbs_belong_status` | Status/stage (planning/development/verification/release, etc.) | string |
| `wbs_states_doing` | Current state / task state | `not_started` / `doing` / `finished` |
| `wbs_role` | Role (can be multiple people) | string |
| `wbs_owner_in_charge` | Person in charge / responsible person (can be multiple people) | userkey; check "I am responsible for" first call `user search` to get userkey |
| `wbs_delay_label` | Delay label | `delay` / `normal` |
| `wbs_milestone_node_type` | Node type | `milestone` / `normal_node` / `key_path_node` |
| `wbs_deletable` | Allow deletion of nodes | bool |

**Recursive child-level SOP**: When the user mentions "child/all children/subordinates", first filter out the target row according to the conditions to get `uuid`, then use `wbs_parent_id` + `In` to check the direct child (multiple uuid separated by commas), and then continue recursing to the next level of uuid until there are no children. **The same tool must be used throughout the entire process** (the draft will always be a draft, and the instance will always be an instance).

### row_field_list returns field control

`row_field_list` (string[]) specifies the return fields as required. When empty, it returns `base.*` + `meta.uuid` by default; `["_all"]` returns the full field.

| wildcard | included fields |
|--------|----------|
| `meta.*` | `uuid`, `parent_id`, related work item information, etc. |
| `base.*` | `name` (row name), `owners` (responsible person), `start_time` / `end_time` (actual start/finish time), `schedule` (scheduling), `schedule_dependency` (scheduling dependency), `union_deliveries` (deliverables), `process_status` (current status) |
| `node_extra.*` | Ordinary node extensions: milestones, belonging states, node unique id `state_key`, pre-order nodes, etc. |
| `sub_instance_extra.*` | Sub-instance extension: dismantle mode `dismantle_mode` |

**Check work item fields/node fields**: First use `workitem meta-fields` to determine whether it is a work item field, and use `workflow meta-node-fields` to determine whether it is a node field; then get the corresponding `workitem_id` / `state_key` from the schedule row, and continue to check the field values ​​based on these IDs.

### wbs list-draft-rows

| Parameters | Type | Required | Description |
|------|------|------|------|
| --work-item-id | string | Yes | Work item ID (**string**); URL is automatically parsed; the name needs to be adjusted first `workitem get` |
| --project-key | string | yes | space key |
| --condition-query | object | No | Filter condition, only supports the fields in the above table |
| --need-structure | boolean | No | Whether to return the tree level. Default `false`; set to `true` when checking/editing children |
| --page-no | number | No | Page number, starting from 1; page turning is required when returning `has_more` |
| --page-size | number | no | Page size, 1–50, default 25. More than 1000 rows need to be merged in pages |
| --row-field-list | string[] | No | See table above |

### wbs list-instance-rows

The parameters are exactly the same as `wbs list-draft-rows`, only the query data set is different (the instance has been published online).

---

## wbs edit-draft

Perform an atomic edit on a draft schedule. Only one `operation_type` can be passed in one call; the operation can internally carry single line or batch editing through arrays such as `items` / `uuids`.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --work-item-id | string | yes | work item ID |
| --project-key | string | yes | space key |
| --operation | object | Yes | Operation object, the structure varies depending on the operation type |

### operation structure

`operation` is an object, uniformly shaped like:

```json
{
"operation_type": "<action PascalCase>",
  "operation_value": {
"<action snake_case>": { /* Action specific fields */ }
  }
}
```

The sub-key under `operation_value` must match `operation_type`; priority is given to passing it in the following table to avoid mechanical snake_case guessing.

| operation_type | operation_value subkey | purpose |
|----------------|------------------------|------|
| `AddTaskRows` | `add_task_rows` | Add common task rows in batches |
| `AddNodeRows` | `add_node_rows` | Add resource node rows in batches |
| `AddSubInstanceRows` | `add_sub_instance_rows` | Add sub-work item rows in batches |
| `AddResourceSubInstanceRows` | `add_resource_sub_instance_row` | Add resource sub-instance rows in batches (note that `row` in the sub-key is singular) |
| `DeleteRows` | `delete_rows` | Batch delete/move rows |
| `RestoreRows` | `restore_rows` | Batch restore rows |
| `UpdateDismantleMode` | `update_dismantle_mode` | Switch disassembly mode |
| `AdjustRowOrder` | `adjust_row_order` | Adjust row ordering / parent |
| `UpdateNodeSequence` | `update_node_sequence` | Modify the sequence of resource nodes |
| `UpdateNodePhases` | `update_node_phases` | Batch modify the phases of nodes |
| `UpdateName` | `update_name` | Modify name |
| `UpdateRoleOwners` | `update_role_owners` | Batch modify role owners |
| `UpdateDelivery` | `update_delivery` | Modify deliverables |
| `UpdatePlannedSchedule` | `update_planned_schedule` | Modify single row schedule (old single row form) |
| `UpdatePlannedSchedules` | `update_planned_schedules` | Modify plans in batches |
| `UpdateSchedulePoints` | `update_schedule_points` | Modify points in batches |
| `UpdateScheduleActualTimes` | `update_schedule_actual_times` | Modify actual working hours in batches |

**Select points**:
- Users use `AddTaskRows` when talking about ordinary tasks/subtasks/task rows; its `parent_uuid` must be the parent row in the current schedule that allows new subtasks, and not any `sub_instance` / `node` / `sub_task` are available. If the backend returns `can not add sub task under parentUUID` or `wbs task not found`, it means that the parent row does not support the new location, and you need to change the parent row or confirm the disassembly structure first.
- Users use `AddSubInstanceRows` when talking about work item types (such as "requirements", "project activities"), and first adjust `workitem meta-types` to confirm `work_item_type_key`.
- When involving resource nodes or resource sub-instances, first call `wbs list-element-templates` query template / `element_key`; usually use `AddNodeRows` for resource nodes and `AddResourceSubInstanceRows` for resource sub-instances.
- `UpdateRoleOwners` and `UpdateDelivery` have full coverage semantics. Before adding a person in charge or a deliverable, use `wbs list-draft-rows` to get the current value and merge the historical value; do not just pass the new value.
- Write the ISO8601 string with time zone for the scheduling time (such as `2026-04-22T00:00:00+08:00`); do not pass the millisecond timestamp, and do not include an additional layer of `schedule` in addition to `specify_schedule`.

**Example: Add common task lines in batches**

```json
{
  "operation_type": "AddTaskRows",
  "operation_value": {
    "add_task_rows": {
"parent_uuid": "<parent row uuid, from wbs list-draft-rows>",
      "pre_uuid": "",
      "items": [
{ "name": "New task name" }
      ]
    }
  }
}
```

**Example: Add resource node rows in batches**

```json
{
  "operation_type": "AddNodeRows",
  "operation_value": {
    "add_node_rows": {
"parent_uuid": "<parent row uuid>",
      "pre_uuid": "",
      "phase": "started",
      "items": [
{ "element_key": "<element_key from wbs list-element-templates>" }
      ]
    }
  }
}
```

**Example: Add sub-work item rows in batches**

```json
{
  "operation_type": "AddSubInstanceRows",
  "operation_value": {
    "add_sub_instance_rows": [
      {
"parent_uuid": "<parent row uuid>",
"work_item_type_key": "<work item type key from workitem meta-types>",
        "fields": [
{ "field_key": "name", "field_value": "Sub work item name" }
        ]
      }
    ]
  }
}
```

**Example: Add resource sub-instance rows in batches**

```json
{
  "operation_type": "AddResourceSubInstanceRows",
  "operation_value": {
    "add_resource_sub_instance_row": [
      {
"parent_uuid": "<parent row uuid>",
"work_item_type_key": "<resource work item type key>",
"resource_work_item_ids": ["<Existing resource instance ID>"]
      }
    ]
  }
}
```

**Example: Modify plan schedule in batches**

```json
{
  "operation_type": "UpdatePlannedSchedules",
  "operation_value": {
    "update_planned_schedules": {
"uuids": ["<line uuid>"],
      "different_schedule": false,
      "specify_schedule": {
        "estimate_start": "2026-04-22T00:00:00+08:00",
        "estimate_finish": "2026-04-25T23:59:59+08:00"
      }
    }
  }
}
```

**Example: Batch modification of estimates/actual hours**

```json
{
  "operation_type": "UpdateSchedulePoints",
  "operation_value": {
    "update_schedule_points": {
"uuids": ["<line uuid>"],
      "different_schedule": false,
      "schedule_point": {
        "schedule_point_value": 3,
        "schedule_point_value_unit": 8
      }
    }
  }
}
```

```json
{
  "operation_type": "UpdateScheduleActualTimes",
  "operation_value": {
    "update_schedule_actual_times": {
"uuids": ["<line uuid>"],
      "different_schedule": false,
      "actual_time": {
        "value": 5,
        "unit": 8
      }
    }
  }
}
```

**Example: Batch delete/restore rows**

```json
{
  "operation_type": "DeleteRows",
  "operation_value": {
    "delete_rows": {
"uuids": ["<line uuid>"],
      "delete_action_type": "delete",
"reason": "Duplicate row"
    }
  }
}
```

```json
{
  "operation_type": "RestoreRows",
  "operation_value": {
    "restore_rows": {
"uuids": ["<line uuid>"]
    }
  }
}
```

**Example: Batch modification stage/person in charge**

```json
{
  "operation_type": "UpdateNodePhases",
  "operation_value": {
    "update_node_phases": {
      "phase": "started",
"uuids": ["<line uuid>"]
    }
  }
}
```

```json
{
  "operation_type": "UpdateRoleOwners",
  "operation_value": {
    "update_role_owners": {
"uuids": ["<line uuid>"],
      "owners": ["<userkey>"]
    }
  }
}
```

If there is `change_uuids` in the response after the call, it can be directly used for the next step of `wbs edit-draft` (such as changing the schedule) or `wbs publish-draft` for partial publishing.

**Suggested Workflow**:
1. `wbs list-draft-rows` gets the `uuid` and current field value of the target row/parent row
2. Call `wbs edit-draft` to perform one operation at a time
3. If the call returns `operation_id`, first use `wbs get-draft-progress` to poll for completion before making the next edit - multiple `wbs edit-draft` may be sent concurrently or without waiting for asynchronous completion, and operations may be lost.
4. Use `wbs publish-draft` to publish all changes.

---

## wbs publish-draft

Post the edited draft online.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-id | string | yes | work item ID |
| --uuid-strings-list | string[] | No | List of row uuids to publish. **Partial release**: Pass the uuid list without secondary confirmation. **Full release**: Do not pass this field (or pass `["_all"]`), you must first confirm it twice with the following **fixed words**, and then execute it after the user agrees |

### Second confirmation of full release (fixed words)

> All edited content by myself and my collaborators will be published. Please confirm whether you want to publish it in full?

Partial publishing (passing in `uuid_strings_list`) **does not require** secondary confirmation and is executed directly.

---

## Asynchronous operation progress

`wbs create-draft` / `wbs edit-draft` / `wbs publish-draft` / `wbs reset-draft` After returning `operation_id`, you need to use `wbs get-draft-progress` to poll the progress. See [misc.md](misc.md#wbs-auxiliary command) for the parameter list.
