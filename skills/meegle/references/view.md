#View auxiliary commands

View search and fixed view management. Use `view get` to read view data (see the SKILL.md main file).

## view search
Search views by name.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --view-scope | string | yes | view scope |
| --key-word | string | yes | keyword |

## view create-fixed
Create a fixed view. Maximum of 200 work items.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --name | string | yes | view name |
| --work-item-type | string | yes | work item type |
| --work-item-id-list | array | yes | list of work item IDs |

## view update-fixed
Update fixed view. add/remove_work_item_ids Choose one of the two.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --view-id | string | yes | view ID |
| --work-item-type | string | yes | work item type |

## view list-multi-project-workitems
View the list of work items that the current user has permissions under the panoramic view (multiProjectView). Panorama views have links with the `multiProjectView` keyword from which the `view_id` can be extracted.

| Parameters | Type | Required | Description |
|------|------|------|------|
| --project-key | string | yes | space key |
| --view-id | string | yes | panorama view ID |
| --page-num | number | No | Paging page number, 50 items per page, starting from 1 |
