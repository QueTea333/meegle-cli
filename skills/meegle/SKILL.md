---
name: meegle
description: |
 [translated text] [translated text] （Meego/Meegle） [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] 。 Use when user needs to work with Feishu/Lark Meego project management — including querying work items, creating/updating work items, completing workflow nodes, checking views, listing todos, analyzing schedules/workloads, or searching with MQL. [translated text] [translated text] ： [translated text] [translated text] 、meego、meegle、 [translated text] [translated text] 、 [translated text] 、 [translated text] 、 [translated text] 、 [translated text] 、 [translated text] 、 [translated text] 、 [translated text] 。
---

# [translated text] [translated text] (Meego/Meegle) [translated text] [translated text]

 [translated text] [translated text] [translated text] Meegle CLI [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] 。

> [translated text] [translated text] [translated text] [translated text] [translated text] [references/api-examples.md](references/api-examples.md)。
> ** [translated text] [translated text] **（ [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ）： [translated text] [references/auth-guard.md](references/auth-guard.md)
> **CLI [translated text] [translated text] **（ [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] ）： [translated text] [references/cli-guide.md](references/cli-guide.md)

---

## Project [translated text] [translated text]

### project search
 [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] project_key [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] --project-key [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] [translated text] [translated text] ）。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --project-key | string | [translated text] | [translated text] projectKey、simpleName [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] |
| --page-num | number | [translated text] | [translated text] [translated text] ， [translated text] 50 [translated text] ， [translated text] 1 [translated text] |

---

## WorkItem [translated text] [translated text]

> [translated text] [translated text] [translated text] [translated text] （`workitem meta-types` / `workitem meta-fields` / `workitem meta-roles` / `workitem meta-create-fields`） [translated text] [translated text] [translated text] [references/workitem.md](references/workitem.md)。

### workitem create
 [translated text] [translated text] [translated text] [translated text] 。** [translated text] [translated text] `workitem meta-fields` [translated text] [translated text] [translated text] ，`workitem meta-roles` [translated text] [translated text] [translated text] 。 [translated text] ID [translated text] [translated text] 。**

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-type | string | [translated text] | [translated text] [translated text] [translated text] |
| --project-key | string | [translated text] | [translated text] [translated text] |
| --fields | array | [translated text] | [translated text] [translated text] [translated text] ， [translated text] [translated text] field_key [translated text] field_value |

### workitem get
 [translated text] ID/ [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] fields [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：`group_type`（ [translated text] [translated text] ）、`description`、`current_status_operator`、`watchers`（value [translated text] null [translated text] [translated text] [translated text] ）； [translated text] [translated text] [translated text] [translated text] `workitem meta-fields` [translated text] key [translated text] [translated text] fields。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-id | string | [translated text] | [translated text] [translated text] ID（ [translated text] `--name` [translated text] [translated text] ） |
| --name | string | [translated text] | [translated text] [translated text] [translated text] [translated text] （ [translated text] `--work-item-id` [translated text] [translated text] ） |
| --project-key | string | [translated text] | [translated text] key |
| --fields | array | [translated text] | [translated text] [translated text] field_key [translated text] field_name； [translated text] `["_all"]` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] `["group_type"]` [translated text] [translated text] [translated text] [translated text] |
| --page-size | number | [translated text] | [translated text] `fields=["_all"]` [translated text] [translated text] ； [translated text] [translated text] [translated text] ， [translated text] 100， [translated text] 200。**Meegle CLI [translated text] [translated text] [translated text] **：`--page-size N` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `need I64 type, but got: STRING`； [translated text] [translated text] `--params '{"page_size":N}'` [translated text] [translated text] [translated text] |
| --page-token | string | [translated text] | [translated text] `fields=["_all"]` [translated text] [translated text] ； [translated text] token， [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] `next_page_token`（token [translated text] [translated text] key， [translated text] `"business"`）； [translated text] ， [translated text] `--params '{"page_token":"..."}'` |

> ** [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] ）**： [translated text] [translated text] `group_id` / `chat_group` [translated text] " [translated text] " [translated text] [translated text] [translated text] [translated text] ** [translated text] ** [translated text] [translated text] [translated text] [translated text] `group_type`。 [translated text] / [translated text] [translated text] [translated text] `group_type`，** [translated text] [translated text] [translated text] [translated text] `group_id` [translated text] `chat_group`**。
>
> ⚠️ ** [translated text] [translated text] [translated text] [translated text] **： [translated text] [translated text] [translated text] ** [translated text] [translated text] `value`**（ [translated text] `type`）， [translated text] [translated text] ** [translated text] [translated text] `type`**—— [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。
>
> [translated text] [translated text] （`workitem_fields[].value` [translated text] ） [translated text] [translated text] ：
> - `auto` → `{value: "auto", label: " [translated text] [translated text] ", group_id: "oc_xxx"}`（ [translated text] [translated text] [translated text] group_id； [translated text] [translated text] [translated text] oc_id [translated text] [translated text] ）
> - `bind` → `{value: "bind", label: " [translated text] [translated text] [translated text] ", group_id: "oc_xxx"}`
> - `disabled` → `{value: "disabled", label: " [translated text] [translated text] "}`（ [translated text] group_id）
>
> [translated text] [translated text] （`field_value` [translated text] JSON）：`{"type": "auto" | "bind" | "disabled", "group_id": "oc_xxx"}`

### workitem +batch-get
 [translated text] [translated text] [translated text] [translated text] （Meegle CLI [translated text] [translated text] fan-out： [translated text] [translated text] `workitem get`）。 [translated text] ≤ 200 [translated text] ID，3 [translated text] ， [translated text] `{results, errors, summary}`；ID [translated text] [translated text] `--format ndjson` [translated text] [translated text] 。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-ids | array | [translated text] [translated text] | [translated text] [translated text] ID [translated text] （ [translated text] [translated text] [translated text] [translated text] [translated text] ） |
| --ids-file | string | [translated text] [translated text] | [translated text] [translated text] [translated text] ID（ [translated text] [translated text] ，`#` [translated text] [translated text] ） |
| --fields | array | [translated text] | [translated text] [translated text] field_key [translated text] |
| --project-key | string | [translated text] | [translated text] key |

### workitem update
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] `workflow update-node`。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-id | string | [translated text] | [translated text] [translated text] ID [translated text] [translated text] |
| --project-key | string | [translated text] | [translated text] key |
| --fields | array | [translated text] | [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] field_key [translated text] field_value |
| --role-operate | array | [translated text] | [translated text] [translated text] ， [translated text] [translated text] op(add/remove)、role_key、user_keys |

** [translated text] [translated text] **： [translated text] [translated text] fields [translated text] [translated text] ， [translated text] [translated text] `role_operate`。role_key [translated text] `workitem meta-roles` [translated text] ，user_keys [translated text] `user search` [translated text] 。

** [translated text] [translated text] [translated text] （`group_type` [translated text] [translated text] ）**： [translated text] [translated text] / [translated text] [translated text] [translated text] [translated text] [translated text] `group_type`， [translated text] [translated text] [translated text] [translated text] `group_id` / `chat_group`。 [translated text] [translated text] `field_value` [translated text] ：`{"type": "auto" | "bind" | "disabled", "group_id": "oc_xxx"}`（ [translated text] [translated text] `type` [translated text] [translated text] [translated text] ，** [translated text] [translated text] [translated text] `value` [translated text] [translated text] **）。 [translated text] [translated text] （ [translated text] [translated text] [translated text] [translated text] [translated text] ）：`bind` [translated text] `group_id` [translated text] [translated text] / [translated text] [translated text] → `group_id is required when group_type=bind`；`auto`/`disabled` [translated text] [translated text] `group_id` → `group_type conflicts with group_id: type=<auto|disabled>`。 [translated text] [translated text] [translated text] [references/sop-update-workitem.md](references/sop-update-workitem.md)。

### workitem query
 [translated text] MQL [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] 6 [translated text] [translated text] ： [translated text] [translated text] [translated text] [references/mql-basics.md](references/mql-basics.md)、 [translated text] [translated text] [translated text] [translated text] [references/mql-operators.md](references/mql-operators.md)、 [translated text] [translated text] [translated text] [references/mql-time-status.md](references/mql-time-status.md)、 [translated text] [translated text] [translated text] [references/mql-people-roles.md](references/mql-people-roles.md)、 [translated text] [translated text] [translated text] [references/mql-nodes-relations.md](references/mql-nodes-relations.md)、 [translated text] [translated text] [translated text] [translated text] [translated text] [references/mql-examples.md](references/mql-examples.md)。 [translated text] MQL [translated text] [translated text] basics [translated text] operators。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --project-key | string | [translated text] | [translated text] [translated text] （ [translated text] [translated text] 、simpleName、projectKey） |
| --mql | string | [translated text] [translated text] | MQL [translated text] [translated text] （ [translated text] SQL）。 [translated text] [translated text] [translated text] 50 [translated text] ；`LIMIT n` [translated text] [translated text] [translated text] （n ≤ 50）， [translated text] [translated text] [translated text] |
| --session-id | string | [translated text] [translated text] | [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] `session_id`； [translated text] [translated text] [translated text] `--project-key` + `--session-id` + `--group-pagination-list`， [translated text] [translated text] `--mql`， [translated text] [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] |
| --group-pagination-list | array | [translated text] [translated text] | [translated text] [translated text] ， [translated text] `--session-id` [translated text] [translated text] ， [translated text] [translated text] 。 [translated text] 50 [translated text] ， [translated text] [translated text] = ceil(`list[0].count` / 50)； [translated text] [translated text] [translated text] [translated text] [translated text] `[{"group_id":"< [translated text] ID>","page_num":N}]` |

** [translated text] [translated text] [translated text] **（ [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] `LIMIT offset, n` [translated text] ）：
1. [translated text] [translated text] `--project-key` + `--mql`， [translated text] [translated text] 1 [translated text] （ [translated text] 50 [translated text] ）。
 [translated text] [translated text] [translated text] `session_id`、`list[0].count`、`list[0].group_infos[].group_id`； [translated text] [translated text] [translated text] `group_id` [translated text] `"1"`。
2. [translated text] [translated text] [translated text] `--project-key` + `--session-id` + `--group-pagination-list '[{"group_id":"< [translated text] ID>","page_num":N}]'`， [translated text] `--mql`。
 `page_num` [translated text] 2 [translated text] ceil(count/50)； [translated text] 1 [translated text] [translated text] [translated text] [translated text] 。
3. [translated text] [translated text] [translated text] `list` / `session_id` [translated text] null， [translated text] [translated text] [translated text] 1 [translated text] ；`session_id` [translated text] [translated text] MQL [translated text] [translated text] [translated text] [translated text] MQL、 [translated text] [translated text] 1 [translated text] 。

** [translated text] **：
- [translated text] `workitem meta-fields` / `workitem meta-roles` [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]
- SELECT [translated text] [translated text] [translated text] [translated text] ，** [translated text] [translated text] [translated text] key**（ [translated text] `name`、`priority`、`status`）； [translated text] [translated text] 50 [translated text] ， [translated text] [translated text] [translated text] [translated text] 「 [translated text] [translated text] [translated text] 」 [translated text] `--session-id` + `--group-pagination-list` [translated text] [translated text]

### workitem list-op-records
 [translated text] [translated text] [translated text] [translated text] [translated text] 。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --project-key | string | [translated text] | [translated text] key |
| --work-item-id | string | [translated text] | [translated text] [translated text] ID |

---

## Attachment [translated text] [translated text]

 [translated text] [translated text] [translated text] “ [translated text] [translated text] [translated text] → [translated text] [translated text] [translated text] → [translated text] [translated text] [translated text] [translated text] ”； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `file` [translated text] `multi-file` [translated text] ， [translated text] [references/attachment.md](references/attachment.md) [translated text] [translated text] [translated text] [translated text] 。Meegle CLI [translated text] `attachment +upload` / `attachment +download` [translated text] [translated text] [translated text] [translated text] [translated text] 。
---

## WorkFlow [translated text] [translated text]

> [translated text] [translated text] [translated text] （`workflow list-state-transitions` / `workflow list-state-required` / `workflow meta-node-fields`） [translated text] [translated text] [translated text] [references/workflow.md](references/workflow.md)。

### workflow transition
 [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-id | string | [translated text] | [translated text] [translated text] ID |
| --action | string | [translated text] | confirm（ [translated text] ） / rollback（ [translated text] ） |
| --node-id | string | [translated text] | [translated text] ID |
| --rollback-reason | string | [translated text] | [translated text] [translated text] ，action=rollback [translated text] [translated text] |
| --project-key | string | [translated text] | [translated text] key |

### workflow transition-state
 [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] 。 [translated text] `workflow list-state-transitions` [translated text] [translated text] [translated text] [translated text] transition_id。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-id | string | [translated text] | [translated text] [translated text] ID |
| --transition-id | string | [translated text] | [translated text] [translated text] ID， [translated text] `workflow list-state-transitions` [translated text] |
| --project-key | string | [translated text] | [translated text] key |

### workflow get-node
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-id | string | [translated text] | [translated text] [translated text] ID [translated text] [translated text] |
| --node-id-list | array | [translated text] | [translated text] ID [translated text] ， [translated text] [translated text] `_all` [translated text] [translated text] [translated text] |
| --field-key-list | array | [translated text] | [translated text] [translated text] key， [translated text] [translated text] `_all` [translated text] [translated text] [translated text] |
| --need-sub-task | boolean | [translated text] | [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] ） |
| --page-num | number | [translated text] | [translated text] [translated text] [translated text] [translated text] 20 [translated text] ， [translated text] [translated text] |
| --project-key | string | [translated text] | [translated text] key |

### workflow update-node
 [translated text] [translated text] （ [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] [translated text] ）。 [translated text] / [translated text] [translated text] [translated text] / [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] 。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-id | string | [translated text] | [translated text] [translated text] ID |
| --node-id | string | [translated text] | [translated text] ID（node_key） |
| --node-owners | array | [translated text] | [translated text] [translated text] [translated text] userkey [translated text] ； [translated text] [translated text] [translated text] `[]` |
| --node-schedule | object | [translated text] | [translated text] [translated text] ， [translated text] `{"estimate_start_date":ms,"estimate_end_date":ms,"owners":[userkey],"points": [translated text] }`； [translated text] [translated text] `{}`； [translated text] [translated text] [translated text] |
| --schedules | array | [translated text] | [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] `estimate_start_date`/`estimate_end_date` [translated text] null |
| --fields | array | [translated text] | [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] `field_key` [translated text] `field_value`（STRING [translated text] ， [translated text] 「 [translated text] [translated text] [translated text] 」） |
| --project-key | string | [translated text] | [translated text] key |

---

## MyWork [translated text] [translated text]

### mywork todo
 [translated text] action [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] MQL [translated text] [translated text] [translated text] / [translated text] 。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --action | string | [translated text] | todo( [translated text] )/done( [translated text] )/overdue( [translated text] )/this_week( [translated text] [translated text] ) |
| --page-num | number | [translated text] | [translated text] ， [translated text] 1 [translated text] ， [translated text] 50 [translated text] |
| --asset-key | string | [translated text] | [translated text] [translated text] key（ [translated text] Asset_xxx）， [translated text] [translated text] [translated text] [translated text] [translated text] |

 [translated text] [translated text] [translated text] ， [translated text] page_num=1 [translated text] [translated text] [translated text] [translated text] [translated text] 。

---

## WorkHour [translated text] [translated text]

> [translated text] [translated text] [translated text] （`workhour list-records`） [translated text] [translated text] [translated text] [references/misc.md](references/misc.md)。

### workhour list-schedule
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --project-key | string | [translated text] | [translated text] key |
| --user-keys | array | [translated text] | [translated text] [translated text] （ [translated text] / [translated text] /userkey），** [translated text] [translated text] 20 [translated text] ** |
| --start-time | string | [translated text] | [translated text] [translated text] ， [translated text] YYYY-MM-DD |
| --end-time | string | [translated text] | [translated text] [translated text] ， [translated text] YYYY-MM-DD，** [translated text] [translated text] [translated text] 3 [translated text] ** |
| --work-item-type-keys | array | [translated text] | [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] `_all` |

** [translated text] [translated text] **： [translated text] [translated text] 20 [translated text] （ [translated text] [translated text] [translated text] [translated text] ）； [translated text] [translated text] ≤ 3 [translated text] （ [translated text] [translated text] [translated text] ）； [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

---

## UserGroup [translated text] [translated text]

> [translated text] [translated text] [translated text] （`team list` / `team list-members`） [translated text] [translated text] [translated text] [references/misc.md](references/misc.md)。

### user search
 [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] / [translated text] [translated text] [translated text] userkey。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --user-keys | array | [translated text] | userKey、Email [translated text] [translated text] ， [translated text] 20 [translated text] |
| --project-key | string | [translated text] | [translated text] key |
| --need-all-status | boolean | [translated text] | [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] false， [translated text] [translated text] [translated text] [translated text] |

### user me
 [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] 。

> **MQL [translated text] ** [translated text] [translated text] `current_login_user()` [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] userkey/ [translated text] [translated text] [translated text] [translated text] ， [translated text] `user me`。

---

## View [translated text] [translated text]

> [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （`view search` / `view create-fixed` / `view update-fixed`） [translated text] [translated text] [translated text] [references/view.md](references/view.md)。

### view get
 [translated text] [translated text] ID [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --view-id | string | [translated text] | [translated text] ID |
| --project-key | string | [translated text] | [translated text] key |
| --page-num | number | [translated text] | [translated text] [translated text] [translated text] |
| --fields | array | [translated text] | [translated text] [translated text] [translated text] |

---

## Comment [translated text] [translated text]

> [translated text] [translated text] [translated text] （`comment list`） [translated text] [translated text] [translated text] [references/misc.md](references/misc.md)。

### comment add
 [translated text] [translated text] 。 [translated text] [translated text] [translated text] Markdown， [translated text] [translated text] [references/rich-text-editor-markdown-syntax.md](references/rich-text-editor-markdown-syntax.md)（ [translated text] @ [translated text] 、 [translated text] 、 [translated text] [translated text] 、 [translated text] / [translated text] [translated text] [translated text] [translated text] ）。

| [translated text] | [translated text] | [translated text] | [translated text] |
|------|------|------|------|
| --work-item-id | string | [translated text] | [translated text] [translated text] ID |
| --content | string | [translated text] | [translated text] [translated text] |

---

## Deliverable [translated text] [translated text]

> [translated text] [translated text] [translated text] ， [translated text] [translated text] [references/misc.md](references/misc.md)。

### deliverable list
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] / [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] ID [translated text] [translated text] 。

---

## Resource [translated text] [translated text]

> [translated text] [translated text] （ [translated text] [translated text] ） [translated text] 。`resource create` [translated text] [translated text] MCP `create_resource_work_item`， [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] / [translated text] [translated text] [translated text] `resource meta-fields`。 [translated text] [translated text] [translated text] [references/misc.md](references/misc.md)。

### resource create
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] ）。 [translated text] [translated text] [translated text] `resource meta-fields` [translated text] [translated text] / [translated text] [translated text] 。

** [translated text] [translated text] **：
- [translated text] [translated text] [translated text] ：`work_item_type_key` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ；`template_id` [translated text] [translated text] [translated text] [translated text] [translated text] ID/ [translated text] ；`fields` / `roles` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。
- [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ： [translated text] [translated text] [translated text] [translated text] [translated text] ID [translated text] `work_item_type_key` [translated text] `template_id`。 [translated text] [translated text] [translated text] `resource create` [translated text] inspect/schema [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] schema [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] 。

---

## WBS [translated text] [translated text]

> [translated text] [translated text] （WBS） [translated text] ** [translated text] （draft）** [translated text] ** [translated text] [translated text] [translated text] （instance）** [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] ：`wbs create-draft` → [translated text] `wbs edit-draft` → `wbs publish-draft`； [translated text] [translated text] [translated text] `wbs reset-draft`。 [translated text] [translated text] [translated text] `wbs edit-draft` [translated text] operation [translated text] [translated text] [references/wbs.md](references/wbs.md)。

### wbs list-draft-rows
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] ：`wbs_name`、`wbs_parent_id`、`wbs_owner_in_charge`、`wbs_states_doing`。 [translated text] [references/wbs.md](references/wbs.md)。

### wbs list-instance-rows
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] `wbs list-draft-rows`。

### wbs edit-draft
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] `operation_type`， [translated text] [translated text] [translated text] [translated text] [translated text] `items` / `uuids` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] / [translated text] / [translated text] / [translated text] / [translated text] / [translated text] [translated text] / [translated text] [translated text] / [translated text] [translated text] / [translated text] [translated text] / [translated text] [translated text] [translated text] ， [translated text] [translated text] [references/wbs.md](references/wbs.md)。
> ⚠️ ** [translated text] **： [translated text] [translated text] [translated text] [translated text] `wbs create-draft`， [translated text] `wbs edit-draft`。 [translated text] [translated text] ： [translated text] `wbs list-draft-rows` [translated text] " [translated text] [translated text] [translated text] " [translated text] [translated text] [translated text] [translated text] [translated text] 。

### wbs publish-draft
 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。
> ⚠️ [translated text] [translated text] [translated text] [translated text] ** [translated text] [translated text] ** [translated text] [translated text] ：" [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] ？"； [translated text] [translated text] （ [translated text] `uuid_strings_list`） [translated text] [translated text] [translated text] 。

---

## [translated text] [translated text] [translated text]

 [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [references/misc.md](references/misc.md)：

- **Chart [translated text] [translated text] ** — `chart get` / `chart list`
- **SubTask [translated text] [translated text] ** — `subtask update`（create/update/confirm/rollback）
- **Relation [translated text] [translated text] ** — `relation list` / `relation meta-definitions`
- **WBS [translated text] [translated text] · [translated text] [translated text] ** — `wbs create-draft` / `wbs reset-draft` / `wbs get-draft-progress` / `wbs list-element-templates`（ [translated text] [references/wbs.md](references/wbs.md)）

---

## [translated text] [translated text] [translated text] （field_value）

> 🚨 **STRING [translated text] **：`field_value` [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] （text/number/bool/option_id/userkey/ [translated text] ） [translated text] [translated text] [translated text] ； [translated text] 、 [translated text] ** [translated text] [translated text] JSON.stringify** [translated text] ， [translated text] [translated text] [translated text] `need STRING type, but got: LIST` / `MAP`。
> [translated text] ：multi-user [translated text] [translated text] [translated text] `"[\"<userkey>\"]"`， [translated text] [translated text] [translated text] `["<userkey>"]`。

| [translated text] [translated text] | [translated text] | field_value [translated text] （ [translated text] [translated text] [translated text] [translated text] [translated text] ） |
|---------|------|------|
| template | [translated text] ID（** [translated text] [translated text] **） | `"<template_id>"` — [translated text] `workitem meta-fields(field_keys=["template"])` [translated text] |
| text / multi-pure-text / link / bool / number | [translated text] [translated text] [translated text] | `" [translated text] [translated text] "` / `"100"` / `"true"` |
| user | [translated text] userkey | `"<userkey>"` |
| multi-user | userkey [translated text] （**stringified**） | `"[\"<userkey1>\",\"<userkey2>\"]"` |
| select / radio / tree-select | [translated text] [translated text] option_id | `"<option_id>"` |
| multi-select | option_id [translated text] [translated text] （**stringified**） | `"[{\"option_id\":\"111\"},{\"option_id\":\"222\"}]"` |
| tree-multi-select | option_id [translated text] [translated text] [translated text] （**stringified**） | `"[\"id1\",\"id2\"]"` |
| multi-text | [translated text] [translated text] Markdown [translated text] [translated text] （ [translated text] [translated text] [references/rich-text-editor-markdown-syntax.md](references/rich-text-editor-markdown-syntax.md)） | `"** [translated text] ** [translated text] "` |
| date | [translated text] [translated text] [translated text] （ [translated text] [translated text] ） | `"1722182400000"` |
| schedule | `[ [translated text] ms, [translated text] ms]`（**stringified**） | `"[1722182400000,1722355199999]"` |
| precise_date | [translated text] （**stringified**） | `"{\"start_time\":1722182400000,\"end_time\":1722355199999}"` |
| workitem_related_select | [translated text] [translated text] [translated text] ID | `"<work_item_id>"` |
| workitem_related_multi_select | ID [translated text] （**stringified**， [translated text] [translated text] ） | `"[<id1>,<id2>]"` |
| role_owners（ [translated text] [translated text] ） | [translated text] - [translated text] [translated text] [translated text] （**stringified**） | `"[{\"role\":\"<role_id>\",\"owners\":[\"<userkey>\"]}]"` |
| signal | option_id [translated text] [translated text] （** [translated text] [translated text] **；MQL [translated text] [translated text] [translated text] label， [translated text] [mql-operators.md §1](references/mql-operators.md)） | `"<option_id>"`（ [translated text] `workitem meta-fields` [translated text] `options[].option_id` [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] 4 [translated text] option：`passed` / `notpassed` / `processing` / `noinformationyet`） |
| compound_field | [translated text] [translated text] [translated text] [translated text] （**stringified** action [translated text] ） | `"{\"action\":\"add\",\"fields\":[[{\"field_key\":\"sub_key1\",\"field_value\":\"v1\"}]]}"` |
| multi_user_compound_field | [translated text] [translated text] [translated text] [translated text] （** [translated text] [translated text] [translated text] [translated text] **；stringified userkey map， [translated text] [translated text] ） | `"{\"userkey1\":[{\"field_key\":\"sub_key1\",\"field_value\":\"v1\"}],\"userkey2\":[]}"` |

> [translated text] [translated text] [translated text] [translated text] fields， [translated text] `workitem update` [translated text] `role_operate` [translated text] 。

### [translated text] [translated text] [translated text] [translated text] （compound_field）

 [translated text] [translated text] [translated text] [translated text] [translated text] `workitem update` [translated text] [translated text] ，`field_value` [translated text] **stringified JSON action [translated text] **。 [translated text] [translated text] JSON object [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `unsupported type: map[string]interface {}, expected type: STRING`。

```json
{"action": "add", "fields": [[{"field_key": " [translated text] [translated text] key", "field_value": " [translated text] [translated text] "}, ...]]}
```

- **action** — [translated text] [translated text] ：`"add"`（ [translated text] [translated text] ）、`"update"`（ [translated text] [translated text] ）、`"delete"`（ [translated text] [translated text] ）
- **fields** — ** [translated text] [translated text] **： [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]
- [translated text] [translated text] `field_value` [translated text] [translated text] [translated text] [translated text] [translated text] STRING [translated text] （text [translated text] [translated text] [translated text] ，multi-user [translated text] stringified [translated text] [translated text] ）
- [translated text] [translated text] key [translated text] `workitem meta-fields` [translated text] [translated text] `compound_field_info` [translated text] [translated text]
- `add` [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] `group_uuid`
- `update` / `delete` [translated text] [translated text] [translated text] `group_uuid` [translated text] [translated text] ， [translated text] [translated text] `group_uuid`，** [translated text] `record_id`**

 [translated text] [translated text] ：

```json
{"action": "update", "group_uuid": " [translated text] [translated text] [translated text] ", "fields": [[{"field_key": " [translated text] [translated text] key", "field_value": " [translated text] "}]]}
```

 [translated text] [translated text] ：

```json
{"action": "delete", "group_uuid": " [translated text] [translated text] [translated text] "}
```

### [translated text] [translated text] [translated text] [translated text] （multi_user_compound_field）

 [translated text] [translated text] [translated text] [translated text] ** [translated text] [translated text] action / group_uuid [translated text] **。 [translated text] [translated text] **stringified JSON map**：key [translated text] [translated text] [translated text] userkey，value [translated text] [translated text] [translated text] [translated text] [translated text] 。

```json
{
  "userkey1": [
 {"field_key": " [translated text] [translated text] key", "field_value": " [translated text] [translated text] "}
  ],
  "userkey2": []
}
```

🚨 ** [translated text] [translated text] ， [translated text] [translated text] [translated text] **。 [translated text] [translated text] userkey [translated text] [translated text] [translated text] [translated text] userkey [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] ：

1. [translated text] `workitem get` [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] map [translated text] key [translated text] [translated text] [translated text] [translated text] ， [translated text] value [translated text] `user`， [translated text] [translated text] [translated text] `child_field_list`
2. [translated text] `workitem meta-fields` [translated text] `compound_field_info`， [translated text] [translated text] [translated text] key、 [translated text] [translated text] [translated text] option_id
3. [translated text] ** [translated text] [translated text] [translated text] userkey** [translated text] map； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text]
4. [translated text] [translated text] map JSON.stringify [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] `workitem get` [translated text] map [translated text] ** [translated text] [translated text] [translated text] [translated text] **。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ；`workitem meta-roles` [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] `editable_personnel_range_type`、 [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] map [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] Skill / CLI [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] `{"userkey":[]}` [translated text] [translated text] —— [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

 [translated text] [translated text] [translated text] [translated text] option_id； [translated text] [translated text] [translated text] `{label, value}` [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] option_id [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] 。

### [translated text] [translated text] [translated text] [translated text] （workitem_related_*）

 [translated text] [translated text] [translated text] [translated text] ID [translated text] ， [translated text] [translated text] →ID [translated text] [translated text] （ [translated text] [translated text] [translated text] + [translated text] ， [translated text] ， [translated text] [translated text] ， [translated text] [translated text] [translated text] ）： [translated text] [references/field-value-extras.md](references/field-value-extras.md)。

---

## [translated text] [translated text] [translated text]

| [translated text] | [translated text] （ [translated text] [translated text] ） |
|------|-------|
| [translated text] [translated text] → project_key | `project search` |
| [translated text] [translated text] / [translated text] / [translated text] | `workitem meta-types` / `workitem meta-fields` / `workitem meta-roles` |
| [translated text] → userkey | `user search`（ [translated text] ≤20） |
| [translated text] [translated text] | `user me`；MQL [translated text] [translated text] `current_login_user()` |
| [translated text] [translated text] / [translated text] [translated text] | `workitem query`（MQL） / `mywork todo` |
| [translated text] [translated text] | `workhour list-schedule`（≤20 [translated text] 、≤3 [translated text] ） |
| [translated text] / [translated text] [translated text] [translated text] | `workitem create` / `workitem update`（ [translated text] fields， [translated text] role_operate） |
| [translated text] [translated text] / [translated text] [translated text] | `workflow transition`（confirm/rollback） / `workflow transition-state`（ [translated text] `workflow list-state-transitions`） |
| [translated text] [translated text] | `view get` |
| [translated text] Meegle [translated text] [translated text] | [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [references/url-links.md](references/url-links.md) |

## [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] “ [translated text] [translated text] / [translated text] [translated text] / [translated text] [translated text] ”， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [references/url-links.md](references/url-links.md)。 [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。


## [translated text] [translated text]

### [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

1. ** [translated text] [translated text] **： [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] 、 [translated text] 、 [translated text] 、 [translated text] [translated text] ； [translated text] URL [translated text] [translated text] `url decode` [translated text] ， [translated text] [references/url-kinds.md](references/url-kinds.md) [translated text] `url_kind` [translated text] [translated text] [translated text] [translated text] SOP [translated text] [translated text] 。** [translated text] ** [translated text] [translated text] URL [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] 「XX [translated text] [translated text] YY [translated text] [translated text] [translated text] 」 [translated text] XX [translated text] [translated text] [translated text] ）。

2. ** [translated text] [translated text] **（ [translated text] [translated text] ）： [translated text] [translated text] [translated text] [translated text] [translated text] （`project search`）、 [translated text] （`workitem meta-types`）、 [translated text] （`user search`）。** [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] **， [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] （`mywork todo`） [translated text] [translated text] ；URL [translated text] `url decode` [translated text] `simple_name` [translated text] [translated text] `project search` [translated text] [translated text] `project_key`（ [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ）。

3. ** [translated text] [translated text] [translated text] **（ [translated text] [translated text] [translated text] ）： [translated text] `workitem meta-fields` [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] [translated text] `field_keys`， [translated text] [translated text] [translated text] `field_query`）。** [translated text] [translated text] [translated text] `--project-key` [translated text] `--work-item-type`**； [translated text] [translated text] [translated text] [translated text] / [translated text] / [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] `workitem meta-roles`。 [translated text] [translated text] [translated text] ： [translated text] [translated text] type=`_work_item_status`（ [translated text] 「 [translated text] / [translated text] / [translated text] 」 [translated text] [translated text] [translated text] ）、 [translated text] [translated text] type=`schedule`（MQL [translated text] `__ [translated text] [translated text] _ [translated text] [translated text] ` / `__ [translated text] [translated text] _ [translated text] [translated text] `）、 [translated text] [translated text] [translated text] key=`priority`。 [translated text] [translated text] [translated text] （ [translated text] project_key + work_item_id， [translated text] `comment add`） [translated text] [translated text] [translated text] 。

4. ** [translated text] **： [translated text] [translated text] [translated text] ， [translated text] [references/performance.md](references/performance.md) [translated text] [translated text] / [translated text] [translated text] 。

5. ** [translated text] [translated text] [translated text] [translated text] **： [translated text] CLI [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] CLI [translated text] [translated text] ， [translated text] [AI [translated text] [translated text] [translated text] [translated text] ](references/ai-handoff.md) [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] CLI [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

### [translated text] [translated text] [translated text]

 [translated text] [references/performance.md](references/performance.md)： [translated text] [translated text] （ [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] ）、 [translated text] [translated text] [translated text] [translated text] [translated text] 。

### Error Handling

** [translated text] **： [translated text] [translated text] [translated text] [translated text] `err_msg` / `inner_err` [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] ；** [translated text] [translated text] [translated text] 2 [translated text] **， [translated text] 3 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

** [translated text] [translated text] **（ [translated text] [translated text] ， [translated text] [translated text] [translated text] ）：
- [translated text] [translated text] [translated text] （`project search` [translated text] 3 [translated text] [translated text] ）
- Permission Denied（ [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ）
- `ai-handoff availability` [translated text] [translated text] [translated text] ， [translated text] `ai-handoff create-link` [translated text] `HANDOFF_REJECTED` / `LOCAL_DISABLED`； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] ） [translated text] [references/error-handling.md](references/error-handling.md)。

---

## AI [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] 、 [translated text] 、 [translated text] 、 [translated text] ， [translated text] [translated text] CLI [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [references/ai-handoff.md](references/ai-handoff.md)。 [translated text] [translated text] ：

- [translated text] [translated text] CLI [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] CLI [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] CLI [translated text] ；
- `available=false` [translated text] `mode=off` [translated text] [translated text] [translated text] ；`mode=auto` [translated text] [translated text] [translated text] ，`mode=ask` [translated text] [translated text] [translated text] [translated text] [translated text] ；
- [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] query [translated text] ID [translated text] ， [translated text] [translated text] [translated text] CLI [translated text] [translated text] [translated text] 。

---

## [translated text] [translated text] （SOP）

 [translated text] [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] SOP：

- [ [translated text] [translated text] [translated text] ](references/sop-create-workitem.md) — [translated text] [translated text] 、 [translated text] 、 [translated text]
- [ [translated text] [translated text] [translated text] ](references/sop-update-workitem.md) — [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text]
- [ [translated text] [translated text] （ [translated text] [translated text] ）](references/sop-transition-node.md) — [translated text] / [translated text] [translated text] 、 [translated text] [translated text]
- [ [translated text] [translated text] （ [translated text] [translated text] ）](references/sop-transition-state.md) — [translated text] [translated text] /issue、 [translated text] bug
