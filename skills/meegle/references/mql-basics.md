# MQL [translated text] [translated text] [translated text] [translated text]

 [translated text] ：1 [translated text] [translated text] · 2 [translated text] [translated text] [translated text] [translated text] · 3 [translated text] [translated text] · 4 [translated text] [translated text] [translated text] [translated text] · 5 [translated text] [translated text] [translated text] [translated text] · 6 [translated text] [translated text] · 7 LIKE [translated text] · 8 [translated text] [translated text] · 9 [translated text] [translated text] · 10 [translated text] [translated text]

## 1. [translated text] [translated text]

```sql
SELECT fieldList FROM `project_key`.`work_item_type_key` [WHERE cond] [ORDER BY field [ASC|DESC]] [LIMIT n]
-- [translated text] [translated text] [translated text] *；FROM [translated text] [translated text] [translated text] ； [translated text] [translated text] 50 [translated text] ，LIMIT n [translated text] [translated text] （n ≤ 50） [translated text] [translated text] ， [translated text] [translated text] --session-id + --group-pagination-list
```

## 2. [translated text] [translated text] [translated text] [translated text]

- [translated text] / [translated text] [translated text] [translated text] ：`` `work_item_id` ``、`` `project_key`.`story` ``。
- `<target:xxx>` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] （`` `name<target:all>` ``）， [translated text] [translated text] [translated text] [translated text] [translated text] lambda [translated text] （`` x.`name<target:all>` ``）； [translated text] SELECT / WHERE [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。
- [translated text] [translated text] [translated text] [translated text] [translated text] （ [translated text] [translated text] [translated text] ）：`= ' [translated text] [translated text] '`、`LIKE '% [translated text] %'`、`= '<id:759…>'`。 [translated text] [translated text] [translated text] [translated text] ，`` = ` [translated text] [translated text] ` `` [translated text] [translated text] [translated text] [translated text] 。
- [translated text] [translated text] [translated text] [translated text] `''`（`' [translated text] '' [translated text] '`）。 [translated text] `\'`： [translated text] [translated text] [translated text] [translated text] ，`' [translated text] \' [translated text] '` [translated text] [translated text] [translated text] ` [translated text] \` [translated text] [translated text] [translated text] 。
- [translated text] [translated text] [translated text] [translated text] ：`'2026-06-01'`， [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] label（ [translated text] [translated text] `workitem meta-fields` [translated text] [translated text] ）， [translated text] [translated text] [translated text] `<id:option_id>`。

## 3. [translated text] [translated text]

| [translated text] | [translated text] | [translated text] |
|------|------|------|
| `SELECT *` | [translated text] [translated text] [translated text] | [translated text] [translated text] [translated text] |
| `count(*)` / `SUM()` / `GROUP BY` | [translated text] [translated text] `count` [translated text] [translated text] [translated text] | [translated text] [translated text] [translated text] |
| `REGEXP` / `regexp_like()` | `LIKE '%...%'` | [translated text] [translated text] [translated text] |
| `currentUser()` / `current_user()` | `current_login_user()` | [translated text] [translated text] [translated text] |
| `CONTAINS(field, val)` | `array_contains()` [translated text] `LIKE` | [translated text] [translated text] [translated text] |
| `NOT BETWEEN a AND b` | `< a OR > b` | [translated text] [translated text] [translated text] [translated text] |
| SELECT [translated text] [translated text] [translated text] WHERE [translated text] [translated text] （`current_login_user()`、`array_contains()`） | [translated text] [translated text] [translated text] [translated text] [translated text] | `status_time()`、`parent_work_item()` [translated text] SELECT [translated text] [translated text] [translated text] [translated text] |

## 4. [translated text] [translated text] [translated text] [translated text] [translated text]

 [translated text] [translated text] SELECT / WHERE [translated text] `unsupported field type`， [translated text] [translated text] [translated text] [translated text] `workitem get`：`attachment` / `file`（ [translated text] ）、`spec_doc` / `specDocs` / `spec_documents`（ [translated text] ）。

 [translated text] ：`multi-file`（ [translated text] `multi_attachment`） [translated text] [translated text] `SELECT` / `IS NULL` / `IS NOT NULL`， [translated text] `key_label_value_list`； [translated text] [translated text] `LIKE` / `array_contains` [translated text] [translated text] [translated text] 。

## 5. [translated text] [translated text] [translated text] [translated text]

MQL [translated text] [translated text] key [translated text] [translated text] ， [translated text] [translated text] key， [translated text] [translated text] [translated text] [translated text] [translated text] 。

| ❌ [translated text] | ✅ [translated text] |
|--------|--------|
| `state_key` / `status` | `work_item_status` |
| `archiving_status` / `is_archived` | `archiving_date`（ [translated text] [translated text] `IS NULL`） |
| [translated text] [translated text] 「 [translated text] 」「 [translated text] [translated text] [translated text] 」「 [translated text] [translated text] 」 | [translated text] `workitem meta-fields` [translated text] key（ [translated text] [translated text] `--project-key` [translated text] `--work-item-type`） |

## 6. [translated text] [translated text] / [translated text] [translated text] [translated text] [translated text]

 [translated text] 、select、tree-select [translated text] [translated text] label [translated text] 「 [translated text] + [translated text] [translated text] [translated text] 」 [translated text] [translated text] 。 [translated text] [translated text] [translated text] ` [translated text] `、` [translated text] [translated text] `、` [translated text] [translated text] `、`OPEN`、`CLOSED`， [translated text] [translated text] `attrValueLabel not found`。
 [translated text] ：`workitem meta-fields` [translated text] options → [translated text] [translated text] label [translated text] `<id:option_id>`。

## 7. LIKE [translated text]

- [translated text] [translated text] `%`。`_` [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] `Internal % and _ characters must be escaped`（ [translated text] `%test_case%` [translated text] [translated text] [translated text] [translated text] `_`）。
- [translated text] [translated text] `_` [translated text] `\_`， [translated text] [translated text] `%` [translated text] `\%`。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `'%...%'`。

```sql
✅ LIKE '% [translated text] [translated text] %' ✅ LIKE '%test\_case%'（ [translated text] [translated text] [translated text] [translated text] ） ✅ LIKE '%100\% [translated text] %'
❌ LIKE '100% [translated text] '（ [translated text] %） ❌ LIKE '%test_case%'（ [translated text] _ [translated text] [translated text] ） ❌ LIKE '%100% [translated text] %'（ [translated text] % [translated text] [translated text] ）
```

## 8. [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] `(v1, v2)`， [translated text] [translated text] `IN` / `NOT IN` / `array_contains` / `=` / lambda [translated text] `IN`。JSON [translated text] [translated text] [translated text] `'["a","b"]'` [translated text] `IN` [translated text] syntax error，`=` [translated text] [translated text] [translated text] [translated text] [translated text] 。

 [translated text] [translated text] ： [translated text] [translated text] [translated text] [translated text] JSON [translated text] [translated text] [translated text] ——`array_intersect(< [translated text] [translated text] >, '["a","b"]')`（ [translated text] [translated text] ） [translated text] `risk_label() = '["a","b"]'`（ [translated text] [translated text] [translated text] ）。

## 9. [translated text] [translated text]

| MQL [translated text] | [translated text] [translated text] [translated text] |
|---------|-------------|
| bool | bool |
| bigint | number [translated text] `work_item_id`、`auto_number` |
| double | [translated text] number |
| varchar | text、multi-pure-text、multi-text、select、tree-select、radio、user、link、signal、workitem_related_select |
| date | date（`YYYY-MM-DD` [translated text] `YYYY-MM-DD+TZD`） |
| datetime | schedule、precise_date（`YYYY-MM-DDThh:mm:ss[TZD]`） |
| array(varchar) | multi-select、tree-multi-select、multi-user、link_cloud_doc、workitem_related_multi_select |
| array(struct) | compound_field |
| lambda | `x -> x IN ('a','b')` [translated text] |

## 10. [translated text] ：heredoc [translated text] shell

MQL [translated text] [translated text] [translated text] 、 [translated text] [translated text] `$` [translated text] [translated text] [translated text] [translated text] [translated text] 。Shell [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] heredoc（`<<'MQL'`）， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] MQL [translated text] [translated text] shell [translated text] [translated text] 。

```bash
meegle workitem query --project-key [translated text] key --mql "$(cat <<'MQL'
SELECT `work_item_id`, `name` FROM ` [translated text] key`.`story` WHERE `name` LIKE '% [translated text] %'
MQL
)" --format json
```
