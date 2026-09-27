# URL Kinds - `url decode` return value to SOP mapping

## Why doesn’t the skill split the URL by itself?

There are many routes in the Meego/Feishu project, and the snake_case old path, `/meego/` prefix, `_xxx_resource` resource work item, preset functional area (user-gantt / chart / multi-project-view, etc.) will make the URL that "looks like the work item details page" actually not. **It is forbidden** to intercept the path segment from the URL as a parameter.

Run one command in unison:

```bash
meegle url decode --url '<URL>' --format json
```

After getting `url_kind`, select SOP or reject according to the form in this article. Pure local analysis, no network calls.

---

## Return fields

| Field | Description |
|---|---|
| `url_kind` | Required; `unknown` if not recognized |
| `simple_name` | Space identifier; when `project_key` is required, first use `project search` with `simple_name` as input |
| `work_item_type` | Work item type api_name (unpackaged by `_xxx_resource`) |
| `work_item_id` | Work item ID (string) |
| `view_id` / `chart_id` / `plugin_key` / `team_id` / `template_id` | Return separately by path |
| `setting_type` | Set page sub-parameters (such as permission type) |
| `edit_str` | Edit status tag of `homepage/edit`, `overview/edit` |
| `is_resource` | true means there is `_xxx_resource` package in the path |
| `query` | Original query parameters, retaining secondary navigation contexts such as `scope/node` |
| `redirected_from` | If alias normalized or `/meego/` prefix stripped, record the original path |
| `pathname` / `host` / `raw` | For diagnostic purposes |

---

## `url_kind` → allowed SOPs

### Work item class

| url_kind | Available fields | Recommended SOP/command |
|---|---|---|
| `workitem_detail` | simple_name · work_item_type · work_item_id | `sop-update-workitem` / `sop-transition-node` / `sop-transition-state` any one, first `project search` → `workitem get` |
| `workitem_create` | simple_name · work_item_type | `sop-create-workitem`（`workitem create`） |
| `workitem_draft` | simple_name · work_item_type | Same as above, prompting the user that this is a draft view |
| `workitem_homepage` / `workitem_homepage_edit` | simple_name · work_item_type | No specific work item ID - **Reject** direct operation, requiring the user to provide the details page URL or work item ID |

### View class (with `view_id` but without `work_item_id`)

| url_kind | semantics | processing |
|---|---|---|
| `view_story` / `view_issue` | Requirements / Defect View | If the user wants to operate "work items in this view", the specific work item URL is required; otherwise, `view get` can be used to view the view |
| `view_multi_project` / `view_project_overview` / `view_user_gantt` | Cross-space/global/Gantt view | Same as above |
| `view_chart` | Chart view | Cross to `chart_*` process |
| `view_workitem` | Universal work item view | If `is_resource=true`, `work_item_type` has been unpacked and can be used directly |

### Chart class

| url_kind | available fields | processing |
|---|---|---|
| `chart_detail` | simple_name · chart_id | Chart details; available with `chart get` |
| `chart_create` | simple_name | Chart creation entry (no ID) |
| `chart_homepage` / `chart_datascope*` / `chart_penetrate*` | simple_name · chart_id? | Drawer/subpage, usually **not** as an operation target |

### Space/setting class (please use OpenAPI for writing operations, not within the scope of this skill)

| url_kind | description |
|---|---|
| `project_home` · `project_overview` · `project_empty` · `project_ai_assist` | Space-level landing page, `simple_name` can be used for `project search` |
| `project_overview_edit` | Editing state, not as an operation target |
| `project_404` · `project_401` · `project_500` | Error page, **denied** |
| `setting_*` | Various setting pages; this skill does not perform setting writing operations, **reject** and inform |
| `setting_other` | Unenumerated setting subpage (rendered internally by the front end via non-exact routes), equivalent to `setting_*` — **denied** |
| `import_jira` · `import_excel` · `data_recycle` | The import/recycle operation is completed within the interface, **rejected** |
| `plugin_page` | Plugin page — behavior defined by plugin, CLI not operable, **denied** |

### Global/Navigation class

| url_kind | processing |
|---|---|
| `workbench` · `workspaces` · `favorites` · `inbox` | Top-level navigation page, **no specific target**, please ask |
| `teams` · `team_detail` | Team page; `team_detail`’s `team_id` can be used in `team list-members` |
| `templates` · `template_detail` · `template_manage` | Template center, this skill does not perform template operations, **rejected** |
| `project_list` | List of all spaces, ask for specific spaces |

### System domain `/b/*`

| url_kind | processing |
|---|---|
| `preference` · `mcp_config` · `mcp_auth` · `ai_hub` · `handover` · `onboarding_*` · `trial_*` · `cross_*` · `slack_connect` · `resource_handover` · `no_project_auth` · `login_datacenter` · `unbundled_register_result` · `b_home` | System/Management page, this skill **deny** business operation |

### Login/External Entrance

| url_kind | processing |
|---|---|
| `login_fetch_cookie` · `login_asset` · `switch_asset` · `home_ka` · `tenant_select` · `tenant_create` · `channel_error` | Login related - change to `auth-guard` |
| `quick_create_form` · `issue_trans` · `issue_create_open_usecase` · `story_create_open` · `jump_to_outer` · `light_share` · `ai_application_share` | Feishu embedded port, this skill is usually not used as the starting point for operations |

### Mistakes

| url_kind | processing |
|---|---|
| `lark_page_404` · `project_empty_page` · `route_loading` · `system_upgrade` | Error page, **denied** |
| `unknown` | **Reject** and ask the user to provide the details page URL or directly describe the task |

### Special field verification

- `redirected_from` is not empty → Mention "Old version path detected and automatically normalized" in the reply to avoid users mistakenly thinking that the URL is wrong
- `is_resource=true` → Inform the user that this is the resource work item view, `work_item_type` has automatically removed the `_xxx_resource` wrapper
- `query.scope` / `query.node` non-empty → only used as navigation context, do not use it as business parameters

---

## Typical branch template (for SOP reference)

```
STEP 0 — URL parsing (only if user provides URL)

  url decode --url "<URL>"
  SAVE $url_kind, $simple_name, $work_item_type, $work_item_id, $view_id, $redirected_from

  SWITCH $url_kind:
- workitem_detail → GOTO STEP 1 of this SOP (already has simple_name + work_item_id)
- workitem_homepage → ASK user: "Requires specific work item URL, this is the type homepage."; STOP
- view_* → ASK user: "This is the view URL, please paste the specific work item details page URL."; STOP
- unknown → ASK user: "The URL cannot be recognized, please confirm and resend or describe the task directly."; STOP
- Others not within the scope of this SOP → inform kind and suggest corresponding actions; STOP
```
