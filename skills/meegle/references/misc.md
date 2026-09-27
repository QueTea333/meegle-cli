#Other low-frequency commands

Summary of parameter tables for low-frequency/single-command small domains. Covers teams, charts, subtasks, relationships, comment queries, time records, deliverables, resource libraries, and WBS auxiliary commands.

---

## Team

### team list
Check out the team list under Spaces.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | no | space key |

### team list-members
View a list of team members.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --team-id | string | yes | team ID |

---

## Metric chart

### chart get
View chart details.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --chart-id | string | yes | chart ID |

### chart list
View the list of charts under the view.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --view-id | string | yes | view ID |

---

## Subtasks

### subtask update
Create/modify/complete/rollback subtasks.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --node-id | string | yes | node ID |
| --work-item-id | string | yes | work item ID |
| --action | string | yes | create/update/confirm/rollback |

---

## relation

### relation list
View a list of associated work items.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-id | string | yes | work item ID |
| --relation-field-key | string | No | Relationship field key, obtained from `relation meta-definitions` |
| --relation-id | string | No | Relationship ID, obtained from `relation meta-definitions` |
| --node-id | string | No | Node ID, passed in when querying the association under a node |
| --page-num | number | No | Paging page number, starting from 1 |
| --page-size | number | No | Number of pages per page, maximum 50 |

### relation meta-definitions
View the relationship definition under the space.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |

---

## Comment query

### comment list
See the list of comments. Add comments using `comment add` (see the main SKILL.md file).

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-id | string | yes | work item ID |

---

## Working hours record

### workhour list-records
View the time registration record of the work item. Team scheduling uses `workhour list-schedule` (see the main SKILL.md file).

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-type | string | yes | work item type |
| --work-item-id | string | yes | work item ID |

---

## Deliverables

### deliverable list
View deliverable details and the root/source work items they belong to.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-ids | string[] | No | Work item ID list; URL is automatically parsed; to provide a name, you need to call `workitem get` first to get the ID |

---

## Resource Library

### resource create
Create a resource template (resource instance) under a resource pool-enabled work item type. First call `resource meta-fields` to get the field/role configuration.

**Creating a resource instance vs creating a normal work item from a resource instance**:
- Create a resource instance: use `resource create`; `work_item_type_key` represents the work item type enabled by the resource library, `template_id` represents the process template, `fields` / `roles` describes the new resource instance itself.
- Create a normal work item from a resource instance: This is the semantics of "derivating/creating a business work item based on an existing resource instance", and the parameters usually require the source resource instance ID. The current command parameters must be based on `inspect` / schema; if there is no explicit source resource instance parameter, do not insert the source resource instance ID into `work_item_type_key`, `template_id` or ordinary fields to guess the call.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-type-key | string | Yes | Work item type key or name; if failed, call `workitem meta-types` first |
| --fields | object[] | No | Resource field list, each item contains field key and field value |
| --roles | object[] | No | Role personnel; if empty, do not specify |
| --template-id | string | No | Workflow template ID or name; if not passed, the first process template of this work item type will be taken |

### resource meta-fields
View the field/role configuration for the repository.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-type-key | string | yes | work item type key or name |

---

## WBS auxiliary command

> See [wbs.md](wbs.md) for the core query/edit/publish commands of the schedule (WBS). This section only contains 4 auxiliary commands: draft life cycle management (create-draft / reset-draft), asynchronous operation progress query (get-draft-progress), and process resource library element query (list-element-templates).

### wbs create-draft
Creates a new draft schedule for the specified work item instance. When you need to edit the schedule but no draft currently exists, first call this tool to create a draft, and then use `wbs edit-draft` to edit it.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --work-item-id | string | Yes | Work item ID, single value; URL automatically parsed |
| --project-key | string | yes | space key |

### wbs reset-draft
Reset the draft to the online instance state, **discarding all unpublished changes**. If `uuids` is not passed, all values ​​will be reset.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --work-item-id | string | yes | work item ID |
| --project-key | string | yes | space key |
| --uuids | string[] | No | List of row uuid to be reset; if empty, all will be reset |

### wbs get-draft-progress
Query the execution progress of asynchronous operations (create/edit/publish/reset) on the draft plan table.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-id | string | yes | work item ID or name |
| --op-type | string | Yes | Operation type: `create` / `edit` / `publish` / `reset` |
| --operation-id | string | Yes | Operation ID (returned by create-draft / edit-draft / publish-draft / reset-draft) |

### wbs list-element-templates
List resource node (node) or resource task (task) templates in the process resource library.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --element-type | string | yes | Repository type: `node` or `task` |
| --project-key | string | yes | space key |
| --work-item-type | string | yes | work item type key or name |
| --page-size | number | no | page size |
| --page-no | number | no | page number |
