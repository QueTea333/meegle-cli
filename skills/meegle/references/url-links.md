# Generate Meegle page link

## Use boundaries

Use only if the user explicitly requests the link, or if the link itself is the deliverable. Do not automatically append links after ordinary query, creation, update, and transfer are completed.

The goal is to deliver a link to a canonical page that can be opened by the current user:

1. First fix the execution context and `$target_host`, and then obtain the effective host through [Auth Guard](auth-guard.md): When the user explicitly selects `--profile`, the commands in Auth Guard and all subsequent queries will pass the same global flag; when not explicitly selected, the current profile will be used throughout the process and midway switching is prohibited. Then confirm that the target object can query the identity; only when the execution context and host are the same in the same round can the successful result be reused.
2. Only use the path templates listed in this article and do not invent routes based on impressions.
3. `url decode` must be called for reverse verification after generation; when the returned `url_kind` is different from the target type or is `unknown`, the link must not be delivered.

Reverse parsing only proves that the link structure is correct. Success of the target query is evidence that "the current identity is accessible"; it does not guarantee that other users also have permissions.

---

## Parameter source

### host

First determine the target host that the user wants to open and save it as `$target_host` for this request before executing Auth Guard:

1. Domain name explicitly designated by the user;
2. The `host` returned by `url decode` of the existing Meegle URL provided by the user;
3. When not specified, SAVE `$target_host = null`, uses the valid `$host` returned and saved by Auth Guard (it has been resolved according to the runtime environment and the selected profile).

Execute Auth Guard before querying the target object, and reuse its host consistency conclusion: the domain name ignores case and removes the `.` at the end, and the port must be consistent. If they are inconsistent, please switch to the profile corresponding to the target environment or log in again; do not use the successful query of environment A to prove that the link of environment B is accessible, and do not use the unnormalized original string to repeat the comparison downstream. The final link can only use the host that successfully queried the actual connection.

When host is empty or is not a trusted Meegle/Feishu project domain name, ask the user and do not guess `meegle.com` or `project.feishu.cn` by default. host only retains the domain name and optional port, without protocol, path, query or fragment; in the end, `https://` will be used uniformly.

### simple_name

`simple_name` and `project_key` are not the same concept, and interchange is prohibited. Use the unique spatial results from `project search` as the authoritative source:

- The `simple_name` of an existing URL can only be used as a spatial query input; it can only be reused when the URL itself points to the same target object/view, or the query results prove that it belongs to the same space as the `project_key` of the target object;
- Only space name or project_key: use `simple_name` in the unique hit result after query;
- The result is not unique or does not return `simple_name`: Ask the user to provide the exact space or link to the existing page, do not use `project_key` instead.

### Other dynamic fields

The ID and type key are first taken from the results of existing commands in this round; otherwise, confirm with the corresponding query command first:

| Goals | Accessibility Check |
|---|---|
| Work item details | `workitem get` |
| Requirements, Defects, General, Space Overview, Gantt, Chart Views | `view get` |
| Cross-space/panoramic view `view_multi_project` | `view list-multi-project-workitems` |
| Chart details | `chart get` |
| Space Home Page / Space Overview | `project search` |

Missing work item types, view IDs, chart IDs, or work item IDs are combined into one query. The query stops when it returns no permission, does not exist, or is not unique, and does not generate an "accessible" link.

A successful view query can only prove that the `view_id` is readable and cannot infer the page routing type alone. `url_kind` must come from the result of `url decode` of an existing URL pointing to the same view, or the authoritative view scope in the query results; the view type dictated by the user can only be used to narrow the scope of the query and cannot be used as routing evidence alone. Ask user if unable to confirm. In particular "overview" must be identified as `view_multi_project` or `view_project_overview`.

---

## Supported canonical paths

Each `{...}` represents only one path segment. Dynamic values ​​are encoded according to RFC 3986 path segment: only letters, numbers, `-`, `.`, `_`, `~` are retained, and the remaining bytes are all percent-encoded; ambiguous input containing `/`, `\\`, `?`, `#`, control characters in the original value, or the entire value is `.` / `..` is rejected.

| url_kind | required field | pathname |
|---|---|---|
| `workitem_detail` | simple_name, work_item_type, work_item_id | `/{simple_name}/{work_item_type}/detail/{work_item_id}` |
| `view_story` | simple_name, view_id | `/{simple_name}/storyView/{view_id}` |
| `view_issue` | simple_name, view_id | `/{simple_name}/issueView/{view_id}` |
| `view_workitem` | simple_name, work_item_type, view_id | `/{simple_name}/workObjectView/{work_item_type}/{view_id}` |
| `view_multi_project` | simple_name, view_id | `/{simple_name}/multiProjectView/{view_id}` |
| `view_project_overview` | simple_name, view_id | `/{simple_name}/project-overview/{view_id}` |
| `view_user_gantt` | simple_name, view_id | `/{simple_name}/userGantt/{view_id}` |
| `view_chart` | simple_name, view_id | `/{simple_name}/workObjectView/chart/{view_id}` |
| `chart_detail` | simple_name, chart_id | `/{simple_name}/chart/detail/{chart_id}` |
| `project_home` | simple_name | `/{simple_name}` |
| `project_overview` | simple_name | `/{simple_name}/overview` |

A resource work item or resource view wraps `work_item_type` as `_{work_item_type}_resource`, for example `story` → `_story_resource`. Do not repack a value that already has this form.

Work item details with `work_item_type=chart` will conflict with `chart_detail`; `work_item_type=chart` in a generic view will conflict with `view_chart`. Backward validation exposes such reserved route conflicts, stopping when encountered and stating that the current canonical route cannot unambiguously express the target.

System pages, settings pages, login pages, import pages, error pages, plugin pages, and any `url_kind` not listed in this article are not supported.

---

## Generation and verification

Each candidate link must verify the complete target identity, not just compare `url_kind`:

- All types: The `host` returned by the decoder must be equal to `$host` after normalization according to the rules of Auth Guard, and `simple_name` must be equal to the authoritative space result;
- `workitem_detail`: `work_item_type`, `work_item_id` must be consistent with the target; the resource work item must also return `is_resource=true`;
- `view_workitem`: `work_item_type`, `view_id` must be consistent with the target; the resource view must also return `is_resource=true`;
- Other `view_*`: `view_id` must be consistent with the target;
- `chart_detail`: `chart_id` must be consistent with the target;
- `project_home` / `project_overview`: no additional IDs except public fields.

Missing or inconsistent fields will stop delivery. Dynamic path segments compare the decoded semantic values, not the percent-encoded text itself.

### Work item details

Known: host=`project.feishu.cn`, simple_name=`xopenapp`, work_item_type=`story`, work_item_id=`7092625364`.

Candidate links:

```text
https://project.feishu.cn/xopenapp/story/detail/7092625364
```

check:

```bash
meegle url decode --url 'https://project.feishu.cn/xopenapp/story/detail/7092625364' --format json
```

Only delivered if the output `url_kind=workitem_detail` and the key field matches the input.

### Generic work item view

```text
https://project.feishu.cn/xopenapp/workObjectView/story/9988
```

Must decode to `view_workitem` and return consistent `simple_name`, `work_item_type` and `view_id`.

### Cross-space/panoramic view

```text
https://project.feishu.cn/xopenapp/multiProjectView/9988
https://project.feishu.cn/xopenapp/project-overview/9988
```

Must decode to `view_multi_project` and `view_project_overview` respectively. Don't just guess one based on the Panorama name; determine the type based on the view query results or an existing URL, and ask the user if you can't tell the difference.

---

## Delivery format and failure handling

When successful, the fixed "type + ID" will be used first as the Markdown label, for example:

```markdown
[Requirement 7092625364](https://project.feishu.cn/xopenapp/story/detail/7092625364)
```

If you must use the name in the query result as a label, first escape Markdown metacharacters such as `\\`, `[`, `]`, `(`, `)` to avoid untrusted names from changing the link structure.

On failure:

- host is missing: please specify the environment domain name or configure the current profile;
- `simple_name` is missing/not unique: please confirm the space, and it is forbidden to replace it with `project_key`;
- The target does not exist or has no permission: it means that the current identity cannot verify accessibility and no pseudo link will be returned;
- decoder returns `unknown` or another `url_kind`: reports route conflict or template not supported, does not return candidate links;
- `url decode` reports unknown command: it means that the local CLI version is too old, it is recommended to upgrade; do not bypass the verification and then hand-deliver.

The above are all deterministic failures; do not retry as is if the input and environment have not changed.
