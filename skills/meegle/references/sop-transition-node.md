# [translated text] [translated text]

> **CRITICAL** — [translated text] [translated text] MUST [translated text] Read [translated text] [translated text] `../SKILL.md`， [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] Error Handling。

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （confirm/rollback）， [translated text] [translated text] [translated text] [translated text] 。

> ** [translated text] **： [translated text] [translated text] [translated text] ** [translated text] [translated text] ** [translated text] [translated text] （ [translated text] [translated text] ）， [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] ）。 [translated text] [translated text] [translated text] [translated text] [translated text] [SKILL.md](../SKILL.md) [translated text] `workflow transition-state`。

---

## [translated text] [translated text] [translated text] ： [translated text] [translated text] + [translated text] [translated text]

`workflow transition` [translated text] ** [translated text] [translated text] node_key（ [translated text] ID）， [translated text] [translated text] [translated text] [translated text] **。 [translated text] [translated text] [translated text] [translated text] `workflow get-node` [translated text] [translated text] →node_key [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] ：

- ** [translated text] [translated text] [translated text] [translated text] ** → `node_id_list` [translated text] [translated text] [translated text] [translated text] `[" [translated text] [translated text] "]`， [translated text] [translated text] [translated text] [translated text]
- ** [translated text] [translated text] " [translated text] [translated text] "** → [translated text] `["_all"]` [translated text] [translated text] ， [translated text] ** [translated text] `field_key_list`**（ [translated text] [translated text] [translated text] ）
- ** [translated text] [translated text] [translated text] **： [translated text] node_key [translated text] [translated text] `workflow transition`
- ** [translated text] [translated text] [translated text] **： [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] [translated text] ） [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text]

> ** [translated text] [translated text] [translated text] [translated text] [translated text] ？** `workflow get-node` [translated text] [translated text] [translated text] （ [translated text] 20 [translated text] [translated text] ）， [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

---

## [translated text] [translated text]

### STEP 1 — [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] work_item_id [translated text] project_key：
- [translated text] [translated text] **URL** → [translated text] `url decode`。 [translated text] `url_kind == workitem_detail` [translated text] [translated text] [translated text] SOP； [translated text] kind [translated text] [url-kinds.md](url-kinds.md) [translated text] [translated text] [translated text]
- [translated text] [translated text] **ID** → [translated text] [translated text] [translated text] project_key
- [translated text] [translated text] [translated text] [translated text]

> **URL [translated text] **：decode [translated text] [translated text] `simple_name` [translated text] [translated text] `project search` [translated text] [translated text] `project_key`（ [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ）。** [translated text] ** [translated text] [translated text] URL [translated text] [translated text] [translated text] [translated text] 。`work_item_id` [translated text] [translated text] [translated text] [translated text] [translated text] 。

### STEP 2 — [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

| [translated text] [translated text] | `node_id_list` [translated text] | `field_key_list` | [translated text] |
|---|---|---|---|
| [translated text] [translated text] [translated text] （ [translated text] " [translated text] [translated text] [translated text] "） | `[" [translated text] [translated text] "]`（ [translated text] [translated text] [translated text] ） | [translated text] | [translated text] [translated text] [translated text] [translated text] |
| [translated text] [translated text] [translated text] [translated text] | `[" [translated text] [translated text] ", " [translated text] [translated text] "]` | [translated text] | [translated text] [translated text] [translated text] [translated text] |
| " [translated text] [translated text] " / [translated text] [translated text] [translated text] | `["_all"]` | [translated text] | [translated text] [translated text] status="doing" |
| " [translated text] [translated text] " / " [translated text] [translated text] " | `["_all"]` | [translated text] | [translated text] [translated text] [translated text] [translated text] |

 [translated text] [translated text] [translated text] [translated text] **name**、**node_key**、**status**（finished/doing/not_started）。

** [translated text] [translated text] [translated text] [translated text] ：**
- " [translated text] "、" [translated text] "、" [translated text] "、" [translated text] " → action = `confirm`
- " [translated text] "、" [translated text] "、" [translated text] " → action = `rollback`
- [translated text] [translated text] → [translated text] `confirm`

** [translated text] [translated text] [translated text] ：**
- [translated text] [translated text] [translated text] [translated text] → [translated text] [translated text] [translated text] [translated text] node_key
- [translated text] [translated text] " [translated text] [translated text] " → [translated text] status = "doing" [translated text] [translated text]
- [translated text] [translated text] " [translated text] [translated text] " → [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]
- [translated text] [translated text] [translated text] → [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]
- [translated text] [translated text] [translated text] → [translated text] `["_all"]` [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]

** [translated text] [translated text] **： [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] " [translated text] [translated text] [translated text] " [translated text] [translated text] [translated text] 。

### STEP 3 — [translated text] [translated text] [translated text]

```bash
meegle workflow transition --work-item-id [translated text] [translated text] ID --project-key [translated text] key --node-id [translated text] node_key --action confirm --rollback-reason '{{rollback_reason}}' --format json
```

** [translated text] [translated text] [translated text] ：**

| [translated text] | [translated text] |
|---|---|
| [translated text] [translated text] | [translated text] [translated text] STEP 6 [translated text] [translated text] |
| [translated text] [translated text] [translated text] [translated text] | [translated text] STEP 4 [translated text] [translated text] |
| [translated text] [translated text] （ [translated text] / [translated text] [translated text] [translated text] ） | [translated text] [translated text] [translated text] [translated text] |

### STEP 4 — [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] ）

 [translated text] [translated text] STEP 3 [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

**4.1 [translated text] [translated text] [translated text] [translated text] [translated text] **

```bash
meegle workflow list-state-required --work-item-id [translated text] [translated text] ID --state-key [translated text] [translated text] node_key --project-key [translated text] key --mode {{mode}} --format json
```

 [translated text] `mode = "unfinished"` [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `form_item_type`（node_field / field） [translated text] `field_type`。

**4.2 [translated text] [translated text] / [translated text] [translated text] （`node_finished_conclusion` / `node_finished_opinion`）**

 [translated text] [translated text] [translated text] 「 [translated text] [translated text] [translated text] / [translated text] 」， [translated text] [translated text] [translated text] ， [translated text] ** [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] **—— [translated text] [translated text] [translated text] 「 [translated text] [translated text] 」 [translated text] ，`workflow get-node` [translated text] `form_items` [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] `node field is invalid`。 [translated text] ** [translated text] `form_items` [translated text] [translated text] [translated text] ， [translated text] **。

** [translated text] **： [translated text] `workflow get-node` [translated text] `field_key_list` [translated text] ， [translated text] `workitem get` [translated text] `fields` [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

```bash
meegle workflow get-node --work-item-id [translated text] [translated text] ID --field-key-list '["node_finished_conclusion","node_finished_opinion"]' --need-sub-task {{need_sub_task}} --page-num {{page_num}} --project-key [translated text] key --node-id-list '[" [translated text] node_key"]' --format json
```

** [translated text] [translated text] [translated text] **： [translated text] [translated text] select [translated text] ， [translated text] `workflow meta-node-fields` [translated text] options， [translated text] [translated text] `option_id`：

```bash
meegle workflow meta-node-fields --field-keys '["node_finished_conclusion"]' --field-types '{{field_types}}' --project-key [translated text] key --work-item-type [translated text] key --query '{{query}}' --format json
```

** [translated text] **： [translated text] `workflow update-node` [translated text] `fields` [translated text] —— [translated text] [translated text] [translated text] `option_id`， [translated text] [translated text] [translated text] 。

> 🚨 ** [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] 。** `workflow update-node` [translated text] `fields` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ，** [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] **（ [translated text] [translated text] `success`）。 [translated text] 「 [translated text] / [translated text] [translated text] [translated text] [translated text] 」 [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] `workflow get-node` [translated text] [translated text] ， [translated text] [translated text] [translated text] success [translated text] [translated text] [translated text] 。

```bash
meegle workflow update-node --work-item-id [translated text] [translated text] ID --node-schedule '{{node_schedule}}' --schedules '{{schedules}}' --fields '[{"field_key":"node_finished_conclusion","field_value":"option_id"}]' --project-key [translated text] key --node-id [translated text] node_key --node-owners '{{node_owners}}' --format json
```

```bash
meegle workflow update-node --work-item-id [translated text] [translated text] ID --node-schedule '{{node_schedule}}' --schedules '{{schedules}}' --fields '[{"field_key":"node_finished_opinion","field_value":" [translated text] [translated text] [translated text] "}]' --project-key [translated text] key --node-id [translated text] node_key --node-owners '{{node_owners}}' --format json
```

**4.3 [translated text] [translated text] ： [translated text] [translated text] [translated text] [translated text] [translated text] **

 [translated text] [translated text] [translated text] **API [translated text] [translated text] **。 [translated text] [translated text] [translated text] [translated text] [translated text] ，** [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ** [translated text] [translated text] [translated text] ：

| [translated text] [translated text] | [translated text] | [translated text] [translated text] |
|---|---|---|
| `actual_work_time` | [translated text] [translated text] | [translated text] [translated text] [translated text] [translated text] |
| `owners_finished_info` | [translated text] [translated text] [translated text] [translated text] [translated text] | [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] |
| `vote-boolean` / `vote-option` / `vote-option-multi` | [translated text] [translated text] | [translated text] [translated text] [translated text] [translated text] |
| [translated text] [translated text] | [translated text] [translated text] [translated text] | [translated text] |

🚨 [translated text] [translated text] [translated text] [translated text] ：
> " [translated text] [translated text] [translated text] 。 [translated text] [translated text] 【 [translated text] [translated text] 】 [translated text] [translated text] [translated text] [translated text] 【 [translated text] [translated text] 】（ [translated text] ：xxx） [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] 。"

**4.4 [translated text] [translated text] [translated text] [translated text] [translated text] **

** [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] ）：**
- [translated text] [translated text] [translated text] [translated text] [translated text] → `user search` [translated text] userkey
- [translated text] [translated text] [translated text] [translated text] [translated text] → [translated text] [translated text] " [translated text] [translated text] [translated text] " [translated text] `current_login_user()`， [translated text] ** [translated text] [translated text] [translated text] [translated text] **
- [translated text] [translated text] [translated text] [translated text] [translated text] → ** [translated text] [translated text] [translated text] **， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]
- [translated text] [translated text] ： [translated text] [translated text] [translated text] " [translated text] [translated text] "/" [translated text] [translated text] " [translated text] [translated text] `current_login_user()`

** [translated text] [translated text] [translated text] **（ [translated text] `workflow update-node` [translated text] [translated text] [translated text] ）：

| field_key | [translated text] [translated text] |
|---|---|
| `owner` (multi-user) | `node_owners` [translated text] 。 [translated text] [translated text] [translated text] → search [translated text] userkey； [translated text] [translated text] " [translated text] " → current_login_user()；** [translated text] [translated text] → [translated text] ** |
| `schedule` | `node_schedule` [translated text] ， [translated text] `{"estimate_start_date": ms, "estimate_end_date": ms, "owners": [userkey], "points": [translated text] }` |
| `point` (number) | `node_schedule` [translated text] `points` [translated text] |

> [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `[]`（ [translated text] `["_all"]`，`["_all"]` [translated text] [translated text] `update_field` [translated text] [translated text] [translated text] [translated text] ）。

> [translated text] [translated text] / [translated text] （`node_finished_conclusion` / `node_finished_opinion`） [translated text] [translated text] [translated text] §4.2： [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] ** [translated text] [translated text] [translated text] [translated text] [translated text] `update-node` [translated text] **（ [translated text] [translated text] [translated text] [translated text] [translated text] ）。

** [translated text] [translated text] [translated text] [translated text] **（ [translated text] [translated text] [translated text] [translated text] [SKILL.md](../SKILL.md)「 [translated text] [translated text] [translated text] 」）：

> 🚨 ** [translated text] [translated text] **： [translated text] [translated text] `field_value` [translated text] [translated text] **STRING**。 [translated text] [translated text] [translated text] [translated text] ； [translated text] / [translated text] ** [translated text] JSON.stringify**， [translated text] [translated text] `need STRING type, but got: LIST`。（ [translated text] 「 [translated text] [translated text] [translated text] 」 [translated text] `workflow update-node` [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] 。）

| field_type | [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] |
|---|---|
| `schedule`（ [translated text] [translated text] ） | **stringified** `"[ [translated text] ms, [translated text] ms]"`；** [translated text] [translated text] [translated text] [translated text] [translated text] **， [translated text] `workflow update-node` [translated text] `node_schedule` [translated text] |
| `signal` | `option_id` [translated text] [translated text] （ [translated text] `workitem meta-fields` [translated text] `options[].option_id` [translated text] ； [translated text] [translated text] `"true"`/`"false"`/`"null"`） |
| `workitem_related_multi_select` | **stringified** ID [translated text] ，** [translated text] [translated text] [translated text] ID**（ [translated text] [translated text] [translated text] ， [translated text] `exists loop` [translated text] ） |
| `tree-select` | [translated text] `"option_id"` [translated text] [translated text] ， [translated text] value/label/children [translated text] JSON |
| `multi-file` | [translated text] [attachment.md](attachment.md) [translated text] [translated text] [translated text] ： [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] |
| `file` | [translated text] [attachment.md](attachment.md) [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] `multi-file` [translated text] [translated text] [translated text] [translated text] [translated text] |

> [translated text] [translated text] [translated text] [translated text] （text / number / bool / user / multi-user / date / precise_date / select [translated text] / multi-text / telephone / email / workitem_related_select [translated text] ） [translated text] [translated text] [translated text] [translated text] [translated text] [SKILL.md](../SKILL.md)「 [translated text] [translated text] [translated text] 」 [translated text] 。

> ** [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ID** [translated text] ， [translated text] [field-value-extras.md](field-value-extras.md)「 [translated text] [translated text] [translated text] [translated text] → ID [translated text] 」 [translated text] [translated text] （ [translated text] [translated text] [translated text] → `workitem query` [translated text] → [translated text] → [translated text] [translated text] [translated text] ） [translated text] 。

** [translated text] [translated text] vs [translated text] [translated text] [translated text] **：
- `form_item_type = "node_field"` → `workflow update-node`， [translated text] `node_id` + `fields`
- `form_item_type = "field"` → `workitem update`（ [translated text] [translated text] [translated text] ）
- [translated text] [translated text] [translated text] `workflow meta-node-fields` [translated text] ； [translated text] [translated text] [translated text] [translated text] `workitem meta-fields` [translated text] （ [translated text] `field_keys` [translated text] [translated text] `field_query` [translated text] [translated text] ， [translated text] [translated text] [translated text] ）

**4.5 [translated text] [translated text] [translated text] [translated text] **

🚨 ** [translated text] [translated text] **： [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] 。

1. ** [translated text] **： [translated text] [translated text] [translated text] （owner）→ `node_owners`； [translated text] / [translated text] → `node_schedule`； [translated text] [translated text] → `fields` [translated text] `workitem update`
2. ** [translated text] [translated text] [translated text] [translated text] / [translated text] [translated text] [translated text] [translated text] **， [translated text] [translated text] [translated text] `workflow update-node`
3. [translated text] [translated text] [translated text] [translated text] `workflow update-node` [translated text] `fields` [translated text] [translated text] [translated text]
4. [translated text] [translated text] [translated text] [translated text] `workitem update` [translated text] `fields` [translated text] [translated text] [translated text]

**4.6 [translated text] [translated text] [translated text] [translated text] **：
- [translated text] [translated text] → ** [translated text] [translated text] **
- [translated text] / [translated text] [translated text] → ** [translated text] [translated text] **
- [translated text] [translated text] → [translated text] [translated text] [translated text] [translated text]
- [translated text] / [translated text] / [translated text] → [translated text] [translated text] [translated text] [translated text] （bool [translated text] false， [translated text] [translated text] 1）
- [translated text] [translated text] [translated text] > 3 [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]

### STEP 5 — [translated text] [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] ，** [translated text] [translated text] `workflow transition`** [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] 2 [translated text] ）。

### STEP 6 — [translated text] [translated text]

 [translated text] [translated text] [translated text] ：

| [translated text] [translated text] | [translated text] | [translated text] | [translated text] |
|---|---|---|---|
| [translated text] [translated text] | confirm | ✅ [translated text] | — |
| [translated text] [translated text] | confirm | ✅ [translated text] | [translated text] [translated text] [translated text] [translated text] 、 [translated text] 、 [translated text] [translated text] |
| [translated text] [translated text] | confirm | ❌ [translated text] | [translated text] [translated text] 「 [translated text] [translated text] 」 [translated text] [translated text] API [translated text] |

 [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

---

## [translated text] [translated text]

 [translated text] [translated text] " [translated text] [translated text] "/" [translated text] [translated text] " [translated text] ：
1. [translated text] [translated text] [translated text] [translated text] [translated text] ： [translated text] [translated text] `workflow transition` → [translated text] [translated text] [translated text] [translated text] → [translated text] [translated text]
2. [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]
3. [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]

---

## [translated text] [translated text] [translated text] （ [translated text] [translated text] ）

> [translated text] [translated text] [translated text] （ [translated text] [translated text] 、 [translated text] [translated text] 、 [translated text] [translated text] [translated text] ） [translated text] [error-handling.md](error-handling.md)。 [translated text] [translated text] SOP [translated text] [translated text] ：

| [translated text] [translated text] | [translated text] [translated text] |
|---------|---------|
| [translated text] [translated text] [translated text] [translated text] | [translated text] `["_all"]` [translated text] [translated text] [translated text] ， [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] |
| [translated text] [translated text] [translated text] | [translated text] STEP 4 [translated text] [translated text] |

---

## [translated text] [translated text] (Circuit Breaker)

> [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] 、 [translated text] [translated text] ） [translated text] [translated text] [SKILL.md](../SKILL.md)「Error Handling」。 [translated text] [translated text] SOP [translated text] [translated text] ：

1. ** [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] **： [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text]
2. ** [translated text] [translated text] [translated text] **： [translated text] [translated text] [translated text] 2 [translated text] [translated text] [translated text]

---

## [translated text] [translated text]

| [translated text] | [translated text] |
|------|------|
| [translated text] [translated text] [translated text] [translated text] | [translated text] [translated text] [translated text] ID [translated text] URL |
| [translated text] [translated text] [translated text] [translated text] | [translated text] [translated text] status="doing" [translated text] [translated text] [translated text] |
| [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] | [translated text] [translated text] `workflow transition-state`， [translated text] [translated text] [translated text] [translated text] [translated text] |
| [translated text] [translated text] " [translated text] [translated text] [translated text] " | [translated text] STEP 4 [translated text] [translated text] |
| [translated text] [translated text] [translated text] [translated text] | [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] |
