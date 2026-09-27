# [translated text] [translated text] [translated text]

> [translated text] [translated text] [translated text] （`{{xxx}}`） [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] `--format json` [translated text] [translated text] [translated text] [translated text] 。

---

## [translated text] [translated text]

### project search

 [translated text] [translated text] key [translated text] ：

```bash
meegle project search --project-key [translated text] [translated text] key --page-num 1 --format json
```

 [translated text] project_key [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] [translated text] ）。

---

## [translated text] [translated text]

### workitem meta-types

```bash
meegle workitem meta-types --project-key [translated text] key --format json
```

### workitem meta-fields

```bash
meegle workitem meta-fields --page-num 1 --project-key [translated text] key --work-item-type story --field-types '{{field_types}}' --field-keys '{{field_keys}}' --field-query '{{field_query}}' --format json
```

### workitem meta-roles

```bash
meegle workitem meta-roles --page-num 1 --project-key [translated text] key --work-item-type story --role-keys '{{role_keys}}' --role-query '{{role_query}}' --format json
```

> **MQL [translated text] [translated text] [translated text] [translated text] **： [translated text] / [translated text] / [translated text] [translated text] [translated text] `workitem meta-fields` [translated text] key [translated text] options； [translated text] [translated text] [translated text] [translated text] `workitem meta-roles` [translated text] `role_id` / `role_name`。 [translated text] [translated text] [translated text] [translated text] 「 [translated text] + [translated text] [translated text] [translated text] 」 [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] 。

### workitem query

 [translated text] ：

```bash
meegle workitem query --project-key [translated text] key --mql "$(cat <<'MQL'
SELECT `work_item_id`, `name`, `work_item_status` FROM ` [translated text] key`.`story` WHERE `archiving_date` IS NULL
MQL
)" --format json
```

 [translated text] [translated text] [translated text] 1 [translated text] （ [translated text] 50 [translated text] ） [translated text] `session_id`、`list[0].count`、`list[0].group_infos[].group_id`。
 [translated text] [translated text] `--project-key` + `--session-id` + `--group-pagination-list`， [translated text] `--mql`；`page_num` [translated text] 2 [translated text] ceil(count/50)。 [translated text] [translated text] `group_id` [translated text] `"1"`：

```bash
meegle workitem query --project-key [translated text] key --session-id [translated text] [translated text] [translated text] session_id --group-pagination-list '[{"group_id":"1","page_num":2}]' --format json
```

 [translated text] [translated text] [translated text] ，group_id [translated text] [translated text] `list[].group_infos[].group_id` [translated text] ：

```bash
meegle workitem query --project-key [translated text] key --session-id [translated text] [translated text] [translated text] session_id --group-pagination-list '[{"group_id":" [translated text] ID","page_num":3}]' --format json
```

### workitem get

 [translated text] [translated text] ：

```bash
meegle workitem get --work-item-id [translated text] [translated text] ID --fields '{{fields}}' --project-key [translated text] key --format json
```

 [translated text] [translated text] [translated text] ：

```bash
meegle workitem get --work-item-id [translated text] [translated text] ID --fields '["group_type"]' --project-key [translated text] key --format json
```

 [translated text] [translated text] [translated text] [translated text] fields [translated text] `["_all"]`。Meegle CLI [translated text] page_size / page_token [translated text] [translated text] `--params` [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] ：

```bash
meegle workitem get --work-item-id [translated text] [translated text] ID --fields '["_all"]' --project-key [translated text] key --params '{"page_size":100}' --format json
```

```bash
meegle workitem get --work-item-id [translated text] [translated text] ID --fields '["_all"]' --project-key [translated text] key --params '{"page_size":100,"page_token":"<next_page_token>"}' --format json
```

### workitem create

 [translated text] [translated text] （ [translated text] [translated text] ）：

```bash
meegle workitem create --work-item-type story --fields '[{"field_key":"template","field_value":" [translated text] ID"},{"field_key":"name","field_value":" [translated text] [translated text] "}]' --project-key [translated text] key --format json
```

 [translated text] [translated text] [translated text] [translated text] / [translated text] [translated text] （role_owners [translated text] stringified JSON）：

```bash
meegle workitem create --work-item-type issue --fields '[{"field_key":"name","field_value":" [translated text] [translated text] "},{"field_key":"template","field_value":" [translated text] ID"},{"field_key":"role_owners","field_value":"[{\"role\":\"reporter\",\"owners\":[\"userkey1\"]},{\"role\":\"operator\",\"owners\":[\"userkey2\"]}]"}]' --project-key [translated text] key --format json
```

> **`role_owners` [translated text] `role` [translated text] [translated text] `role_id`**（`workitem meta-roles` [translated text] [translated text] [translated text] key， [translated text] `operator` / `reporter` / `role_xxxxxx`）， [translated text] `role_name`。 [translated text] [translated text] `role_id`： [translated text] [translated text] `reporter`、 [translated text] [translated text] `operator`。 [translated text] [translated text] [translated text] `role_id` [translated text] [translated text] ，** [translated text] [translated text] `workitem meta-roles` [translated text] **。 [translated text] [translated text] [translated text] `fields` [translated text] `role_owners`； [translated text] [translated text] [translated text] [translated text] [translated text] ** [translated text] ** [translated text] `workitem update` [translated text] `role_operate`。

### workitem update

 [translated text] [translated text] [translated text] ：

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":"priority","field_value":"option_id"}]' --format json
```

 [translated text] multi-user（ [translated text] [translated text] stringified）：

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":"current_status_operator","field_value":"[\"userkey1\",\"userkey2\"]"}]' --format json
```

 [translated text] [translated text] [translated text] [translated text] [translated text] `add` / `update` / `delete` action：

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":" [translated text] [translated text] key","field_value":"{\"action\":\"add\",\"fields\":[[{\"field_key\":\" [translated text] [translated text] key\",\"field_value\":\" [translated text] [translated text] \"}]]}"}]' --format json
```

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":" [translated text] [translated text] key","field_value":"{\"action\":\"update\",\"group_uuid\":\" [translated text] [translated text] [translated text] \",\"fields\":[[{\"field_key\":\" [translated text] [translated text] key\",\"field_value\":\" [translated text] \"}]]}"}]' --format json
```

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":" [translated text] [translated text] key","field_value":"{\"action\":\"delete\",\"group_uuid\":\" [translated text] [translated text] [translated text] \"}"}]' --format json
```

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":" [translated text] [translated text] [translated text] key","field_value":"{\"userkey1\":[{\"field_key\":\" [translated text] [translated text] key\",\"field_value\":\" [translated text] [translated text] \"}],\"userkey2\":[]}"}]' --format json
```

 [translated text] [translated text] [translated text] （group_type [translated text] [translated text] ）：

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":"group_type","field_value":"{\"type\":\"auto\"}"}]' --format json
```

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":"group_type","field_value":"{\"type\":\"bind\",\"group_id\":\"oc_xxx\"}"}]' --format json
```

```bash
meegle workitem update --work-item-id [translated text] [translated text] ID --project-key [translated text] key --fields '[{"field_key":"group_type","field_value":"{\"type\":\"disabled\"}"}]' --format json
```

`auto` / `disabled` [translated text] [translated text] group_id。

> ** [translated text] [translated text] [translated text] **： [translated text] userkey [translated text] [translated text] map [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

---

## [translated text] [translated text]

### user search

 [translated text] [translated text] [translated text] ：

```bash
meegle user search --user-keys '[" [translated text] "," [translated text] "]' --project-key {{project_key}} --need-all-status false --format json
```

 [translated text] [translated text] / [translated text] ：

```bash
meegle user search --user-keys '[" [translated text] "]' --project-key {{project_key}} --need-all-status true --format json
```

### user me

```bash
meegle user me --format json
```

---

## [translated text] [translated text]

### mywork todo

```bash
meegle mywork todo --action todo --page-num 1 --asset-key {{asset_key}} --format json
```

`--action`：`todo` / `done` / `overdue` / `this_week`。

---

## [translated text] [translated text]

### workhour list-schedule

```bash
meegle workhour list-schedule --start-time 2025-03-01 --end-time 2025-03-31 --project-key [translated text] key --user-keys '[" [translated text] "," [translated text] "]' --work-item-type-keys '{{work_item_type_keys}}' --format json
```

### workhour list-records

```bash
meegle workhour list-records --project-key [translated text] key --work-item-type story --work-item-id [translated text] [translated text] ID --page-num 1 --format json
```

---

## [translated text] [translated text]

### view get

```bash
meegle view get --view-id [translated text] ID --project-key [translated text] key --fields '{{fields}}' --page-num {{page_num}} --format json
```

---

## [translated text] [translated text]

### workflow get-node

```bash
meegle workflow get-node --work-item-id [translated text] [translated text] ID --field-key-list '{{field_key_list}}' --need-sub-task {{need_sub_task}} --page-num {{page_num}} --project-key [translated text] key --node-id-list '[" [translated text] ID [translated text] _all"]' --format json
```

### workflow transition（ [translated text] [translated text] ）

```bash
meegle workflow transition --work-item-id [translated text] [translated text] ID --project-key [translated text] key --node-id [translated text] ID --action confirm --rollback-reason '{{rollback_reason}}' --format json
```

### workflow transition-state（ [translated text] [translated text] ）

```bash
meegle workflow transition-state --work-item-id [translated text] [translated text] ID --project-key [translated text] key --transition-id [translated text] ID --format json
```

### workflow list-state-transitions

```bash
meegle workflow list-state-transitions --work-item-id [translated text] [translated text] ID --work-item-type story --user-key userkey --project-key [translated text] key --format json
```

### workflow list-state-required

```bash
meegle workflow list-state-required --work-item-id [translated text] [translated text] ID --state-key [translated text] [translated text] key --project-key [translated text] key --mode unfinished --format json
```

---

## [translated text] [translated text]

### comment add

```bash
meegle comment add --work-item-id [translated text] [translated text] ID --content ' [translated text] [translated text] ' --project-key {{project_key}} --format json
```

### comment list

```bash
meegle comment list --work-item-id [translated text] [translated text] ID --project-key [translated text] key --page-num {{page_num}} --start-time {{start_time}} --end-time {{end_time}} --format json
```

---

## [translated text] [translated text]

### relation meta-definitions

```bash
meegle relation meta-definitions --project-key [translated text] key --work-item-type {{work_item_type}} --relation-work-item-type {{relation_work_item_type}} --format json
```

### relation list

```bash
meegle relation list --project-key [translated text] key --work-item-id [translated text] [translated text] ID --page-size {{page_size}} --relation-field-key {{relation_field_key}} --node-id {{node_id}} --relation-id {{relation_id}} --page-num {{page_num}} --format json
```

---

## [translated text] [translated text]

### subtask update

```bash
meegle subtask update --node-id [translated text] ID --project-key {{project_key}} --task-id {{task_id}} --assignee '{{assignee}}' --work-item-id [translated text] [translated text] ID --role-assignee '{{role_assignee}}' --fields '{{fields}}' --schedule '{{schedule}}' --action create --deliverable '{{deliverable}}' --format json
```
