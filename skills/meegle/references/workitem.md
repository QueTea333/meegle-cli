# Work item metadata command

Auxiliary commands for querying work item types, fields, and role configurations. Used to confirm legal keys before `workitem create` / `workitem update` / `workitem query`.

## workitem meta-types
Get a list of all work item types in the specified space. Use this command to confirm the legal type_key when the user description is ambiguous.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space projectKey |

## workitem meta-fields
Gets the available field configurations (excluding disabled fields and role configurations) for the specified space and work item type.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-type | string | yes | work item type key or name |
| --page-num | number | Yes | Number of pages, 50 items per page, starting from 1 |
| --field-keys | array | No | Exactly match field key or name |
| --field-query | string | No | Fuzzy query field key and name |
| --field-types | array | No | Filter by field type |

## workitem meta-roles
Gets the list of roles for the specified work item type. Used to confirm the legal role_key before querying/creating/updating work items.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-type | string | yes | work item type key or name |
| --page-num | number | Yes | Number of pages, 50 items per page, starting from 1 |
| --role-keys | array | No | Exactly match role key or name |
| --role-query | string | No | Fuzzy query role key and name |

## workitem meta-create-fields
View the fields and types available when creating work items.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --work-item-type | string | Yes | The type of work item to create key |
