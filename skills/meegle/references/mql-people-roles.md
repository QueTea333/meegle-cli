# MQL personnel functions, name disambiguation and role columns

Table of Contents: 1 People and Team Functions · 2 Name Disambiguation `<id:xxxx>` · 3 Role Column

## 1. Personnel and team functions

| function | return |
|------|------|
| `current_login_user()` | Current login user userkey |
| `team(include_manager, 'Team name')` | Team member userkey array; the first parameter `true` includes the manager |
| `all_participate_persons()` | All participants userkey array |
| `participate_persons()` | Current participant userkey array |
| `participate_roles()` | `role_name` label array of participating roles |

constraint:

- The team name must be queried using `team list` (pass `--project-key`), otherwise `attribute_value not found` will be reported.
- The value bit of `participate_roles()` must use `role_name` label (`'Backend Development``), and it is forbidden to pass `role_id` (`'fe_rd``).
- The value of the personnel field must be `<id:userkey>` (see §2). `current_login_user()` is an MQL built-in function literal and will not be parsed by `user search`.

```sql
array_contains(`current_status_operator`, current_login_user()) -- I am currently in charge
any_match(`current_status_operator`, x -> x IN (team(true, 'real team name'))) -- assigned to a team (including managers)
array_contains(participate_roles(), 'Back-end development', 'Front-end development') -- There are specified roles to participate in
```

## 2. Name disambiguation `<id:xxxx>`

### 2.1 Personnel field value bit

The value bits of personnel fields, user, multi-user, custom personnel controls, and role columns must use `<id:userkey>` first:

- Naked userkey (`= 'example_userkey'`) is used as name resolution in some scenarios, and `user label '...' does not exist` is reported.
- Naked Chinese names (`= 'Zhang San'`) often report `user label '...' is not unique` (even if `user search` is unique, the server still checks globally).

Process: `user search` takes userkey → write `<id:userkey>` in the value bit.

```sql
WHERE `owner` = '<id:userkey>' -- Single person field
WHERE array_contains(`current_status_operator`, '<id:userkey>') -- Multi-person field: = or array_contains can be used
WHERE `current_status_operator` IN ('<id:k1>', '<id:k2>') -- multi-person OR preferred IN
WHERE `current_status_operator` = current_login_user()
```

### 2.2 Team/Enumeration Disambiguation

Use `<id:xxxx>` when the same name is repeated:

```sql
WHERE any_match(`current_status_operator`, x -> x IN (team(true, 'Open Platform Team<id:3455>')))
WHERE `priority` = '<id:option_2>'
```

## 3. Role column

Roles are not fields and are referenced as columns in MQL.

| Writing | Usage scenarios | Examples |
|------|---------|------|
| Main writing method `` `__<role_name>` `` | Default. `role_name` is strictly taken from `workitem meta-roles`, including spaces, keep them as they are | `` `__Backend Development` ``, `` `__Manager` `` |
| fallback `` `__role_<project_key>_<work_item_type>_<role_id>` `` | Only when `role_name` conflicts with other fields/role Chinese names | `` `__role_<project_key>_story_<role_id>` `` |

- Hard prefix: MQL involving roles must first `workitem meta-roles` (pass `--project-key` and `--work-item-type` at the same time) to get the real `role_name` / `role_id`; it is forbidden to spell the column name according to the user's spoken language, abbreviations, and typos.
- System default roles: `role_name` handler/reporter; `role_id` `operator` / `reporter` (the latter is only used for the `role_operate` parameter and is prohibited from appearing in MQL column names or functions).
- Invalid form (Code 3010 `attr label not found`): `__<role_id>` (`__fe_rd`, `__operator`), `<role_id>` ` (without `__`), function wrapper `role(fe_rd)` / `get_role_owners(fe_rd)`.

```sql
WHERE array_contains(`__Backend Development`, '<id:userkey>') -- ✅ Main writing method
WHERE array_contains(`__role_<project_key>_story_<role_id>`, '<id:userkey>')  -- ✅ fallback
WHERE `Manager` = '<id:userkey>' -- ❌ Missing __ / Original Chinese text
WHERE array_contains(`__operator`, '<id:userkey>') -- ❌ used role_id
```
