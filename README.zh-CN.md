# Meegle CLI

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/node-%3E%3D16-brightgreen.svg)](https://nodejs.org/)
[![npm version](https://img.shields.io/npm/v/@lark-project/meegle.svg)](https://www.npmjs.com/package/@lark-project/meegle)

[English](./README.md) | [Simplified Chinese](./README.zh-CN.md)

Feishu Project ([Meegle](https://meegle.com?utm_source=github&utm_medium=readme&utm_campaign=meegle_cli) / [Lark Project](https://project.feishu.cn?utm_source=github&utm_medium=readme&utm_campaign=meegle_cli)) command line tool. Manage Work Items, view schedules, and search data in the terminal without opening a browser.

[Installation](#install) · [Quick Start](#quickstarthumanuser) · [Agent Skill](#ai-agent-skill) · [Command](#command_list) · [Enterprise Extension](#enterprise-cli-extension) · [Authentication](#Authentication) · [Configuration](#Configuration) · [Security](#Security&Risk Tips) · [Contribution](#contribution)

## Why choose Meegle CLI?

- **Agent Friendly** — The installation wizard will install the built-in AI Agent Skill and feed the Meegle Operation manual to mainstream Agents such as Trae, Claude Code, Cursor, Windsurf, and Gemini CLI. CLI commands are designed for both humans and agents, structured JSON output, `--dry-run` preview, `--device-code` without TTY process
- **Complete coverage** — 16 business domains (Work Item, Workflow, Subtask, Comment, Work Hour, Relation, My Work, View, Chart, Team, User, Space, Attachment, Deliverable, Resource Library, WBS Plan Table), 50+ commands mapped to Meegle core capabilities
- **Two-layer Parameters model** - Use `--flag-name` for daily use, which is light and direct. For complex loads (such as `fields[]`), use `--params <json>` for complete coverage - Choose the appropriate granularity according to the scenario
- **Flexible output format** — Supports `json` / `table` / `ndjson` / `raw`, with `--select` point path projection can be directly piped to other tools
- **Secure by default** — Credentials are stored in the system keychain, `${VAR}` environment variable template prevents secrets from being placed in the config file, and multiple profiles are separated into staging / prod
- **Support enterprise expansion** - Enterprises can build their own distributions by referencing the public `cmd`, Credential, Transport, and Platform packages during compilation without the need to Fork this repository.

## Function overview

| Domain | Capabilities |
| -------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| 📋 [Work Item](#workitem--Work Item field) | Create, view, Update, batch read, MQL Query, view Operation records, read metadata |
| 🔀 [Workflow](#workflow--Workflow domain) | Node and State flows, UpdateNodeField, list available flows and RequiredField |
| ✅ [Subtask](#subtask--Subtask) | Create, Update, Complete, Rollback Subtask |
| 💬 [Comment](#comment--Comment field) | Add and list Comments on Work Item |
| ⏱️ [Work Hour](#workhour--Work Hour field) | List Work Hour records and view Team member schedules |
| 🔗 [Relation](#relation--relationship domain) | List RelationWork Item and view RelationType definition |
| 📌 [My Work](#mywork--workbench domain) | View this week / Completed / Overdue |
| 👁️ [View](#view--View domain) | Create and Update fixed View, SearchView by name |
| 📊 [Chart](#chart--measurement field) | List Charts under View and view ChartDetails |
| 👥 [Team and User](#team--user--person domain) | List Team, Team members, Search users, and view current login identity |
| 🗂️ [Space](#project--space domain) | Search space by keyword |
| 📎 [Attachment](#attachment--Attachment domain) | Two-stage upload/download protocol - `prepare-*` basic command + `+upload` / `+download` end-to-end shortcut command |
| 📦 [Deliverable](#deliverable--Deliverable domain) | List Deliverable and its root Work Item and source Work Item |
| 🧩 [Resource Library](#resource--Resource Library) | Create resource template, view Resource LibraryConfiguration |
| 🗓️ [WBS Plan Table](#wbs--wbs-Plan Table) | Query draft / Published plan line, Create / Edit / Publish / Reset draft, Query draft progress, column process Resource Library element |
| 🔐 [Authentication and Configuration](#Authentication) | OAuth login, Device Code process, multi-profile Configuration, environment variable injection |
| 🔗[URL parsing](#url--url-parsing) | Offline parsing Feishu Project/Meegle URL, output `url_kind` + structured Field |
| 🤖 [Agent Skill](#ai-agent-skill) | Built-in skills for Trae / Claude Code / Cursor / Windsurf / Gemini / Copilot |

## Install

### Preconditions

- Node.js >= 16 (comes with `npm` / `npx`)

Run the installation wizard:

```bash
npx @lark-project/meegle@latest install
```

The wizard will globally install or upgrade the CLI, install the AI ​​Agent Skill, configure Meegle host, and initiate login.

### Automatic Update prompt

The CLI distributed via npm checks for the latest version of npm at most every 24 hours when launched in an interactive terminal. After a new version is discovered, the `Added` and `Changed` entries that have been released between the current version and the latest version will be extracted from [CHANGELOG](https://github.com/larksuite/meegle-cli/blob/main/CHANGELOG.md#changed), and an interactive What's New prompt will be displayed:

```text
✨ New Meegle CLI version available: v<current version> → v<latest version>

What's New:
v<version number>
• Added: <functional summary>

❯ Update now (recommended)
  Remind me later
  Use ↑/↓ to select, press Enter to confirm
```

Directly pressing Enter will first execute `npm install -g @lark-project/meegle@latest` Update CLI, then use the same installation method best-effort Update Meegle Agent Skill used by `meegle install`, and finally continue the original command. When the current environment lacks a suitable Skill installer, the installer is incompatible, or the Skill download fails, it will only prompt that it has been skipped, and the successful CLI Update will not be judged as a failure. After selecting **Remind me later**, there will be no more checks within 24 hours. This lightweight Update process does not reconfigure the host or trigger a login; you can still run `meegle install` when you need a complete one-stop setup.

In non-interactive/CI environments, stdout is taken over by pipes, and when executing `install` or Shell completion commands, the check will be skipped to ensure that the structured command output remains unchanged. It can also be turned off explicitly by setting `MEEGLE_NO_UPDATE_CHECK=1`.

## Quick start (human users)

> **Tips for AI Agent:** If you are completing this installation of AI Agent for the user, please jump directly to [Quick Start (AI Agent)] (#Quick Start ai-agent--ci--headless environment) - there are non-interactive commands you need.

```bash
# 1. Install CLI + Skill, Configuration host, and log in
npx @lark-project/meegle@latest install

# 2. View this week’s to-do list
meegle mywork todo --action this_week --page-num 1

# 3. View help
meegle --help
meegle workitem --help

# 4. View command ParametersDetails
meegle inspect workitem.create
```

## Quick Start (AI Agent / CI / Headless Environment)

The default browser OAuth flow relies on real TTYs. In environments such as CI Runner, Pipeline, and Claude Code, use the same installation wizard, but explicitly pass in the host and enable Device Code login:

```bash
npx -y @lark-project/meegle@latest install --host <host> --device-code --lang zh
```

`<host>` Example: `project.feishu.cn`, `meegle.com`, or self-created tenant domain name (such as `your-tenant.example.com`).

The Device Code process will output the authorization URL; send the link to the user and keep the command running until the user completes authorization in the browser.

verify:

```bash
meegle auth status
```

For completely unattended CI (no real person participation), please use environment variables to inject tokens instead, see [Sandbox/CI](#Sandbox--ci directly injects environment variables).

## AI Agent Skill

The installation wizard will install `skills/meegle/`, which is a skill that can be directly loaded into Agents such as Trae, Claude Code, Cursor, Windsurf, Gemini CLI, GitHub Copilot CLI, etc. It will teach the Agent to operate Meegle through this CLI instead of guessing the command shape from the natural language Description.

### Cover content

- **Command Reference** — RequiredParameters and Example for each `meegle` resource/method
- **MQL Search** — syntax, operators, scope keywords of `workitem query`
- **Field value** — How to write complex Field payloads (arrays, nested JSON, date ranges)
- **Rich Text** — A subset of Markdown supported by the Meegle rich text editor
- **SOP** — Step-by-step scripts for CreateWork Item, Node flow, State flow, UpdateField and other scenarios
- **Authorization Guard** — All business commands must be preceded by `meegle auth status`

### How to use

After the installation wizard is completed, you can directly talk to the Agent in natural language. For example:

```
Help me take a look at the P0 requirements in this week’s to-do list in the PROJ space
```

The Agent will refer to the skill and automatically select the appropriate `meegle` command to execute. Combined with `--dry-run` (see [Security](#Security and Risk Tips)), the request can be previewed before the Agent actually executes the side effects.

## Command list

### workitem — Work Item field

| Command | Description |
|------|------|
| `workitem create` | CreateWork Item |
| `workitem get` | View Work Item overview |
| `workitem +batch-get` | Read Work Item in batches by ID (the client side does fan-out on `workitem get`; the `+` prefix represents the scene/syntactic sugar command) |
| `workitem update` | Modify Work ItemField |
| `workitem query` | Using MQL SearchWork Item |
| `workitem list-op-records` | View Operation records |
| `workitem meta-types` | View Work ItemTypeList |
| `workitem meta-create-fields` | Field available when viewing Create |
| `workitem meta-fields` | View FieldConfiguration |
| `workitem meta-roles` | View RoleConfiguration |

### workflow — Workflow domain

| Command | Description |
|------|------|
| `workflow transition` | Transfer or rollback Node |
| `workflow transition-state` | Flow State flow State |
| `workflow get-node` | View NodeDetails |
| `workflow update-node` | Modify Node |
| `workflow meta-node-fields` | View NodeFieldConfiguration |
| `workflow list-state-transitions` | View transferable State |
| `workflow list-state-required` | View flow Required information |

### subtask — Subtask

| Command | Description |
|------|------|
| `subtask update` | Create/Modify/Complete/Rollback Subtask |

### comment — Comment field

| Command | Description |
|------|------|
| `comment add` | Add Comment |
| `comment list` | View CommentList |

### workhour — Work Hour field

| Command | Description |
|------|------|
| `workhour list-records` | View Work Hour registration records |
| `workhour list-schedule` | View personnel schedule |

### relation — relational domain

| Command | Description |
|------|------|
| `relation list` | View the Work Item of Relation |
| `relation meta-definitions` | View Relation relationship definitions |

### mywork — workbench domain

| Command | Description |
|------|------|
| `mywork todo` | View my to-do/done |

### view — View domain

| Command | Description |
|------|------|
| `view create-fixed` | Create fixed View |
| `view get` | View ViewDetails |
| `view update-fixed` | Update fixed View |
| `view search` | SearchView by name |
| `view list-multi-project-workitems` | View the Work ItemList under Panoramic View (multiProjectView) |

### chart — Measurement field

| Command | Description |
|------|------|
| `chart get` | View ChartDetails |
| `chart list` | View ChartList under View |

### team/user — Personnel field

| Command | Description |
|------|------|
| `team list` | View TeamList under space |
| `team list-members` | View Team members |
| `user me` | View the currently logged in user information |
| `user search` | Search user information |

### project — spatial domain

| Command | Description |
|------|------|
| `project search` | Search space information |

### attachment — Attachment field

| Command | Description |
|------|------|
| `attachment prepare-upload` | Upload preprocessing - returns signed object storage URL and sharding plan |
| `attachment prepare-download` | Download preprocessing - returns signed object storage URL and sharding plan |
| `attachment +upload` | End-to-end upload: preprocessing + signing HTTP POST, returning `file_token` and file meta information |
| `attachment +download` | End-to-end download: preprocessing + signed HTTP GET + atomic write file, used to consume the `file_url` returned by `workitem get` / `comment list` |

### deliverable — Deliverable field

| Command | Description |
|------|------|
| `deliverable list` | List Deliverables and their root Work Item and source Work Item |

### resource — Resource Library

| Command | Description |
|------|------|
| `resource create` | Create resource template (resource instance) under Work ItemType with Resource Library enabled |
| `resource meta-fields` | View Resource LibraryConfiguration (resource Field, resource Role) |

### wbs — WBS Plan Table

| Command | Description |
|------|------|
| `wbs list-draft-rows` | Filter rows by condition in the schedule draft and return the specified Field |
| `wbs list-instance-rows` | Filter rows by condition in the published online schedule instance and return the specified Field |
| `wbs create-draft` | Create a new schedule draft for the specified Work Item instance |
| `wbs edit-draft` | Execute an atomic operation (Added/Delete/Restore/Sort/Rename/Change person in charge/Change schedule) on a single line of the schedule draft, the OperationType is specified through `--params` |
| `wbs publish-draft` | Publish the edited draft online |
| `wbs reset-draft` | Reset the draft to the online instance State, discarding all unpublished changes |
| `wbs get-draft-progress` | Query Schedule Draft Operation (Create/Edit/Publish) execution progress |
| `wbs list-element-templates` | List resource Nodes and resource task templates in the process Resource Library |

### ai-handoff — AI assistant relay

This set of commands is registered locally by the CLI and does not belong to the MCP tool. `availability` is an optional pre-check before preparing `query` or Relation context; expected business rejection is a successful detection and returns stable `reject_code` and `reject_msg` for display only. Dependencies or transport exceptions follow the CLI common error model.

`availability` reads the Handoff Configuration and its `mode` (`off`, `ask`, `auto`) in the general `GET /goapi/v5/meeglecli/config` detection result. The CLI caches the complete Configuration snapshot returned successfully by profile for up to 1 hour, and does not cache dependencies or transport exceptions. When `preference handoff auto|ask|off` is successful, `auth login`, and `create-link` are rejected by the server, the Configuration cache will be invalidated immediately. `create-link` does not read the cache: it always double-checks on the server and creates the link directly.

The create-link HTTP response always contains `available`: Normally, HTTP 200 and `available=true + url` are returned; the CLI then only replaces the host (including the Configuration port) of the URL with the current login domain name, retaining the scheme, path, query and fragment, so that the link always falls in the currently selected Meegle or Feishu Project environment. If the business is expected to be unavailable, HTTP 200 and `available=false + reject_code + reject_msg` will be returned, and the local Configuration cache will be cleared at the same time; unexpected exceptions will be responded to by the interface's general error response.

Set `MEEGLE_AI_HANDOFF=disabled` to hard disable Handoff in the current installation. At this time, `availability` and `create-link` whose Parameters are legal will return `available=false`, `reject_code=LOCAL_DISABLED` and `reject_msg` which is only for display. It does not require login, does not read the availability cache, and does not call the Handoff API. If not set or the value is not `disabled`, the original server-side control behavior is maintained. Local access control can only turn off functions, and cannot forcibly turn on functions by bypassing server-side business switches, rights, or personal preferences.

| Command | Description |
|------|------|
| `ai-handoff availability` | Check business switches, grayscale, AI assistant benefits, personal preferences and linked service availability |
| `ai-handoff create-link` | Generate AI assistant link based on Required `--query` and optional, repeatable Typed `--related-context` JSON object |

```bash
meegle ai-handoff availability --format json

MEEGLE_AI_HANDOFF=disabled meegle ai-handoff availability --format json

meegle ai-handoff create-link --params '{
"query": "Summary of risks and suggestions for next steps",
  "related_context": [{
    "type": 3,
    "work_item": {
      "project_key": "PROJ",
      "work_item_type_key": "story",
      "work_item_id": "123"
    }
  }]
}' --format json

meegle ai-handoff create-link --help
meegle inspect ai-handoff create-link
```

Each `related_context` element must have `type` set, and only one payload matching Type must be set. The Facade contract only uses the business identifier and does not expose the common `key` of the AI ​​service; the Field is only completed when converted to the internal `query + entities` contract:

| Type | Payload | RequiredField | OptionalField |
|------|---------|----------|----------|
| `1` Project | `project` | `project_key` | — |
| `3` WorkItem | `work_item` | `project_key`、`work_item_type_key`、`work_item_id` | — |
| `4` View | `view` | `project_key`、`view_id` | `work_item_type_key` |
| `5` MeasureChart | `measure_chart` | `project_key`、`chart_id` | — |

ContextType `2` is an enumeration value reserved by IDL for the WorkItemType context that is currently not supported. Please do not use or reuse it.

Each attempt has independent timeout protection, and retryable transmission failures (timeouts, HTTP 5xx/429, network errors) will be retried up to 3 times with exponential backoff and jitter. `create-link` will carry a stable idempotent key that remains unchanged across these retries, so retries will not create a second link. Policy, Parameters checksum and 4xx errors will fail quickly without retrying. The Parameters error envelope returned by Facade will be mapped to `HANDOFF_API_INVALID_PARAM` and `retryable=false`, including the case where the number of queries or contexts exceeds the negotiated upper limit. The CLI does not expose the Facade's internal biz error id, cause or chain, only returns a concise message and recommends checking the current limit via `ai-handoff availability`.

### preference — personal preference

Handoff Suggestions preferences are stored in the server-side unified user preference service and do not receive Project or tenant Parameters; when there is no user override value, it defaults to `auto`. The general preference writing interface uses `preferences` List, the current item's `type=handoff_suggestions`, and the payload is `{"mode":"off|ask|auto"}`; success only means that the writing is completed. Read the current Configuration through the CLI config detection interface. After the schema is successfully written, the local Configuration cache will be invalidated so that the changes will take effect during the next detection.

| Command | Description |
|------|------|
| `preference handoff auto` | Automatically display AI Handoff recommendations |
| `preference handoff ask` | Show AI Handoff Ask before recommending |
| `preference handoff off` | Turn off AI Handoff recommendation |

Use `meegle preference handoff --help` to see mode behavior, and `meegle inspect preference handoff auto` (or `ask` / `off`) to see the command-level ParametersDescription.

MVP does not enable `reset` until the preference service provides atomic unset capability.

### auth — Authentication domain

| Command | Description |
|------|------|
| `auth login` | Login (browser or `--device-code`) |
| `auth logout` | log out |
| `auth status` | Check the login status (a verification request will be sent to the server to confirm that the token is actually available) |

### config — Configuration field

| Command | Description |
|------|------|
| `config init` | Initialize Configuration |
| `config show` | View the current Configuration |
| `config set` | Set Configuration items |
| `config get` | Read Configuration items |
| `config profile create\|list\|use\|current\|delete` | Manage multiple environment profiles |

### url — URL parsing

An offline, purely local URL parsing tool that does not initiate any network calls. When skill or pipeline parses Feishu Project/Meegle URL, this command will be used first, and `url_kind` will be used as a branch to avoid guessing the field from the original path.

| Command | Description |
|------|------|
| `url decode --url <URL>` | Parse the URL into fields such as `url_kind` + `simple_name` / `work_item_type` / `work_item_id` / `view_id` / `chart_id` / `query` / `redirected_from`; unrecognized URL returns `url_kind: "unknown"`. |

### Other commands

| Command | Description |
|------|------|
| `version` | Output CLI version number (`meegle --version` is an alias) |
| `inspect [command]` | View command ParametersDetails |
| `completion bash\|zsh\|fish` | Generate Shell completion script |
| `completion install` | Automatically install Shell completion |

## Commonly used Example

### View to-do

```bash
#To-do this week
meegle mywork todo --action this_week --page-num 1

# Things done
meegle mywork todo --action done --page-num 1

# Overdue items
meegle mywork todo --action overdue --page-num 1
```

If `mywork todo` returns `get action info fail`, refresh the command metadata first:
`meegle --refresh mywork todo --action this_week --page-num 1`. If the account belongs to multiple workspaces at the same time,
Please explicitly pass in the workspace key:
`meegle mywork todo --action this_week --page-num 1 --asset-key Asset_xxx`。

### QueryWork Item

```bash
# View Work Item overview
meegle workitem get --work-item-id 12345

# View NodeDetails of Work Item
meegle workflow get-node --work-item-id 12345 --need-sub-task
```

### Read Work Item in batches

`workitem +batch-get` will call `workitem get` separately for each ID and aggregate the results into a single response.
Shared flags (such as `--project-key`) are applied to every per-item call. The command starts with `+`,
Indicates that it is a scenario/syntax sugar command on the client side. The CLI combines batch semantics on top of `get`, and there is no corresponding
Single backend interface.

```bash
# Pass in multiple IDs separated by commas in one call
meegle workitem +batch-get --project-key PROJ --work-item-ids "12345,12346,12347"

# Read IDs from file (one per line, lines starting with '#' are treated as comments)
meegle workitem +batch-get --project-key PROJ --ids-file ./ids.txt

# Output one line of JSON for each item, summary as the last line
meegle workitem +batch-get --project-key PROJ --work-item-ids "12345,12346" -o ndjson
```

Response structure (JSON):

```json
{
  "summary": { "total": 3, "succeeded": 2, "failed": 1 },
  "results": [
    { "work_item_id": 12345, "data": { /* ... */ } },
    { "work_item_id": 12346, "data": { /* ... */ } },
    { "work_item_id": 12347, "error": { "code": "...", "message": "..." } }
  ]
}
```

Constraints: Maximum 200 IDs in a single call, fixed 3 worker concurrency. Partial failure **doesn't** interrupt the entire batch -
Please judge by `summary.failed` or the per-item `error` Field; returning 401 from the server will terminate the entire batch.

### CreateWork Item

```bash
# Pass fields[] via --params
meegle workitem create --project-key PROJ --work-item-type story \
  --params '{"fields":[
{"field_key":"name","field_value":"Optimize login process"},
    {"field_key":"priority","field_value":"P1"}
  ]}'

# Complex Field values ​​(arrays, nested JSON) also use --params
meegle workitem create --project-key PROJ --work-item-type story \
  --params '{"fields":[
{"field_key":"name","field_value":"scheduled task"},
    {"field_key":"schedule","field_value":[1722182400000,1722355199999]}
  ]}'
```

### Modify Field

```bash
# Modify Work Item name
meegle workitem update --work-item-id 12345 \
--params '{"fields":[{"field_key":"name","field_value":"New title"}]}'

# Modify multiple fields at the same time
meegle workitem update --work-item-id 12345 \
  --params '{"fields":[
{"field_key":"name","field_value":"new title"},
    {"field_key":"priority","field_value":"P0"}
  ]}'
```

### Attachment upload/download

The `attachment` domain splits Feishu Project's two-stage Attachment protocol into two layers of exposure:

- **Basic commands** (`attachment prepare-upload` / `attachment prepare-download`)
Returns signed URL preprocessing results - suitable for writing your own scripts for HTTP transmission or debugging sharding plans.
- **Shortcut commands** (`attachment +upload` / `attachment +download`) combine basic preprocessing with
Signed object storage HTTP POST/GET is combined into an end-to-end process on the client side. The `+` prefix indicates that this is
Scenario Commands - The CLI concatenates preprocessing output with byte transfers on the client side.

`--resource-type` tells the backend what scenario this upload/download is for:

| `--resource-type` | Purpose |
|-------------------|------|
| `15` | Work ItemAttachmentField |
| `16` | Picture in Work Item Rich Text Field |
| `13` | CommentAttachment |
| `14` | Pictures in Comment text |

**Upload scope**: Each upload requires one of `--work-item-id` or `--work-item-type`.
**As long as the Work Item already exists, `--work-item-id`** will be used first (update / comment scenario);
Only use `--work-item-type` when creating with Attachment, such as "Work Item does not exist yet". When both are passed on,
`--work-item-id` wins, `--work-item-type` is silently discarded.

```bash
# Upload files for Work ItemAttachmentField (resource-type 15)
meegle attachment +upload ./a.pdf \
  --resource-type 15 \
  --project-key PROJ --work-item-id 12345 --field-key files_field

# Create with Attachment scene - Work Item does not exist yet, use --work-item-type
meegle attachment +upload ./a.pdf \
  --resource-type 15 \
  --project-key PROJ --work-item-type story --field-key files_field

# Upload images for rich text Field (resource-type 16)
meegle attachment +upload ./diagram.png \
  --resource-type 16 \
  --project-key PROJ --work-item-id 12345 --field-key spec_field

# Upload Attachment for Comment (resource-type 13)
meegle attachment +upload ./report.pdf \
  --resource-type 13 \
  --project-key PROJ --work-item-id 12345

# Upload images for Comment (resource-type 14)
meegle attachment +upload ./screen.png \
  --resource-type 14 \
  --project-key PROJ --work-item-id 12345

# Download: Directly pass in the opaque file_url in the response of other commands.
URL=$(meegle workitem get --project-key PROJ --work-item-id 12345 \
        --fields files_field --format json \
      | jq -r '.fields.files_field[0].url')
meegle attachment +download "$URL" \
  --project-key PROJ --work-item-id 12345 \
  --output ./local.pdf --overwrite
```

**Integrity check (`+download`)**: `+download` will do an additional integrity check on the downloaded file.
If the verification fails or cannot be verified, the download will be aborted and no files will be written. Returns when verification fails
`CLIENT_FILE_SIGN_MISMATCH` error (`CLIENT_FILE_SIGN_UNVERIFIED` when it cannot be verified);
Both are temporary issues, just try again.

**Custom header/environment routing**: The custom header of the current profile configuration will act on both download GET and preprocessing calls.
This uses the environment routing header to pin the entire download to the same environment. The auth related header will be stripped before GET.
Ensure that the token is not sent to the host where the object storage is located.

`+upload` outputs a JSON object, including file_token and file meta information:

```json
{
  "file_token": "...",
  "file_url": "https://...",
  "name": "a.pdf",
  "size": 12345,
  "mime_type": "application/pdf"
}
```

To combine the results into downstream commands, use `jq` or a scripting language to extract the required fields:

```bash
# CommentAttachment - comment add directly receives file_token
TOKEN=$(meegle attachment +upload ./report.pdf --resource-type 13 \
        --project-key PROJ --work-item-id 12345 | jq -r '.file_token')
meegle comment add --work-item-id 12345 --content "See Attachment" --file-token "$TOKEN"
```

**Field level recirculation format** (reference when splicing `--fields`):

- **Work ItemAttachmentField** (`--resource-type 15`) - `field_value` is a JSON *string*,
After parsing, it is `[{"name","type","size","fileToken"}]`. NOTE: `fileToken` is camelCase (other
The backend fields are all snake-shaped), and `size` is a string, not a number.
- **Rich text Field / Comment image** (`--resource-type 16` / `14`) —— for images
Markdown embedding of `![name](file_url) <!-- file_token -->`.
- **CommentAttachment** (`--resource-type 13`) —— `comment add --file-token` is received directly
  `file_token`。

### MQL Search

```bash
# P0 requirements under Query space
meegle workitem query --project-key PROJ \
--mql "SELECT \`name\`, \`priority\` FROM \`space name\`.\`Requirement\` WHERE \`priority\` = 'P0'"
```

### Check the schedule

```bash
# Check Team member schedule
meegle workhour list-schedule --project-key PROJ \
  --start-time 2026-03-01 --end-time 2026-03-31 \
--user-keys "Zhang San, Li Si, Wang Wu"
```

### View user information

```bash
meegle user search --user-keys "Zhang San, Li Si" --project-key PROJ
```

##Parameters passed

### Basic flag

Parameters for each command are passed via `--flag-name`:

```bash
meegle workitem get --work-item-id 12345 --project-key PROJ
```

If multiple Required flags or positional Parameters are missing in one call, the CLI will use the same command in the order of command definition.
`CLIENT_MISSING_REQUIRED` lists all missing items in the error; if only one input is missing, the original singular copy will be retained.

```text
$ meegle workflow list-state-transitions --project-key demo --work-item-id 1 --dry-run
missing required parameters: --user-key, --work-item-type
```

### --set key=value (general)

`--set` is an **alternative writing** for ordinary flag, and only affects **top-level Parameters**. When the inferred value Type is consistent with the command schema, `--set key=value` is equivalent to `--key value`. It is suitable for using unified key=value syntax in scripts, or writing nested top-level objects through dot-path. Legal JSON numbers are retained as exact numbers (including large integers, high-precision decimals, and exponential forms), `true` / `false` are converted to booleans, and other values ​​remain strings.

```bash
# The following two are equivalent:
meegle mywork todo --action this_week --page-num 1
meegle mywork todo --set action=this_week --set page_num=1

# Exactly preserved JSON numbers without float64 or int64 conversion:
--set work_item_id=9007199254740993

# is not a legal JSON number, so the string "01" remains:
--set external_id=01

# dot-path nesting (rarely used by Meegle, but supported):
--set extra.flag=true # becomes {"extra":{"flag":true}}
```

Whether provided via a named flag, `--params` or `--set`, scalar numeric Parameters declared by the interface schema use the same precise representation: `number` accepts any legal JSON number; `integer` also requires that the value is mathematically an integer, but is not restricted by Go's `int64` range. When the schema requires scalar numbers or integers, non-JSON number writing such as `+1`, `01`, and non-numeric JSON values ​​will be rejected before the request is sent. Explicit CLI control Parameters such as paging sequence numbers will still perform range verification as needed.

`--set` only writes **top-level Parameters** and **will not** write to `fields[]` of Work Item. To write `fields[]` please use `--params '{"fields":[...]}'` (see below).

### --params JSON

`--params` receives a JSON object with each top-level key merged in as a CLI flag.
The top-level key can use the `snake_case` name of MCP Parameters or the `kebab-case` name of CLI flag;
It must be valid Parameters of the current command, not a free-structured payload.

```bash
# The following two are equivalent:
meegle workitem get --work-item-id 12345 --project-key PROJ
meegle workitem get --params '{"work_item_id":12345,"project_key":"PROJ"}'
```

When to use `--params`:

- The value is a nested object or array (`fields[]`, `schedule{}`), writing flag directly is too awkward
- Want to batch transfer multiple Parameters at once, or load the payload from a file (see `@file.json` below)

The top-level Parameters of Required can also be provided through `--params`, which is equivalent to directly passing in the corresponding flag.

```bash
meegle workitem create --project-key PROJ --work-item-type story \
--params '{"fields":[{"field_key":"name","field_value":"title"}]}'
```

#### Common misuse: not all names are top-level flags

Some values ​​that look like top-level Fields are actually the Field values ​​of Work Item and must be wrapped in `fields[]`
inside rather than on top. For example, when `workitem update`, `priority` belongs to Work ItemField.
Flag that is not a command:

```bash
# ❌ "priority" is not a legal flag for workitem update - the CLI will output a warning on stderr and the backend will ignore it.
meegle workitem update --work-item-id 12345 --params '{"priority":"P1"}'

# ✓ Field values ​​should be wrapped in fields[]
meegle workitem update --work-item-id 12345 \
  --params '{"fields":[{"field_key":"priority","field_value":"P1"}]}'
```

Top-level keys that are not in the tool declaration flag list will appear as
`validation.unknown_params` is listed, and a line will be printed to stderr when running.
warning. They will still be forwarded to the backend unchanged (preventing local tool schema cache staleness
Misjudgment, use `--refresh` to refresh if necessary).

Via `meegle workitem meta-fields --project-key PK --work-item-type TK`
Query the legal `field_key` under a certain Work ItemType.

#### Read from file (`@file.json`)

The inline JSON experience on Windows is terrible: CMD must write `"` as `\"`, and PowerShell swallows another layer when passing escaped backslashes to native commands. Add `@` prefix to `--params` to change it to read from a file. Various shells of macOS, Linux, and Windows can work consistently:

```bash
# body.json:
# {"fields":[{"field_key":"name","field_value":"Optimize login process"}]}

meegle workitem create --project-key PROJ --work-item-type story \
  --params @body.json

# Absolute paths are also supported
meegle workitem update --work-item-id 12345 --params @/tmp/patch.json

# PowerShell usage is the same without any escaping
meegle workitem create --project-key PROJ --work-item-type story --params '@body.json'
```

The system default encoding is used for reading, and both relative paths and absolute paths are accepted. If the file does not exist, `PARAM_INVALID` will be returned; if the file content is not legal JSON, `INVALID_PARAMS_JSON` will be returned.

### Priority

When `--set`, `--params` and ordinary flag are used together:

1. Ordinary CLI flag overrides the top-level key with the same name in `--params` / `--set`
2. `--set` overwrites the top-level key with the same name in `--params`

### ArrayParameters

Multiple values ​​are separated by commas:

```bash
--user-keys "Zhang San, Li Si, Wang Wu"
--field-keys "name,status,priority"
```

### Boolean Parameters

Adding flag directly means true, not adding it means false:

```bash
meegle workflow get-node --work-item-id 12345 --need-sub-task
```

## Global Flag

| Flag | Abbreviation | Description |
|------|------|------|
| `--format` | `-o` | Output format: `json` (default), `table`, `ndjson`, `raw` |
| `--select` | | Field projection (supports dot path) |
| `--set` | | Set nested Parameters (repeatable) |
| `--params` | `-P` | Complete JSON Parameters body; starting with `@` is changed to read from the file (such as `--params @body.json`) |
| `--dry-run` | | Only render the request, not actually execute it |
| `--envelope` | | Wrap the successful output as `{data, meta, error}` - hang on `meta.logid` when the backend returns logid |
| `--verbose` | `-v` | Verbose output |
| `--profile` | | Specify Configuration profile |
| `--refresh` | | Refresh the local command cache from the server (bypasses the 24-hour cache) |
| `--auto-paginate` | | Automatically pull and merge all pages when the response contains a paging signal (`next_page_token` or `pagination.has_more`); the List array will be spliced, with a safe upper limit of 200 pages and 3 consecutive empty page protection to prevent runaway loops |
| `--version` | | Print CLI version number and exit (alias for `meegle version`) |

Only as an independent Flag, `--version` represents a version alias; if the preceding string Flag is waiting for a Parameters value, the literal `--version` will remain the value of that Flag.

## Advanced usage

### Output format

```bash
# JSON (default)
meegle workitem get --work-item-id 12345

# NDJSON (suitable for pipeline processing)
meegle mywork todo --action this_week --page-num 1 -o ndjson

# sheet
meegle mywork todo --action this_week --page-num 1 -o table
```

### `--select` Field projection

`--select` press `.` to perform Field projection in segments; the next segment after the array will broadcast the remaining path to each element in the array and retain the original nested structure.

| expression | response | projection result |
|---|---|---|
| `list` | `{"list":[{"a":1}], "total":1}` | `{"list":[{"a":1}]}` |
| `list.a` | `{"list":[{"a":1,"b":2},{"a":3,"b":4}]}` | `{"list":[{"a":1},{"a":3}]}` |
| `list.a,list.b` | Same as above | `{"list":[{"a":1,"b":2},{"a":3,"b":4}]}` (Multiple paths are merged by clicking the subscript) |
| `list.work_item_info.work_item_name` | `{"list":[{"work_item_info":{"work_item_name":"x"}}]}` | `{"list":[{"work_item_info":{"work_item_name":"x"}}]}` |
| `nodes.0` | `{"nodes":[{"id":"a"},{"id":"b"}]}` | `{"nodes":{"0":{"id":"a"}}}` (Number of Fields by index) |

```bash
# Top level selection
meegle workitem get --work-item-id 12345 --select "id,name,status"

# Broadcast across arrays - extract Field from nested records
meegle mywork todo --action done --page-num 1 \
  --select "list.work_item_info.work_item_name,list.state_info.end_state_key_name"

# Top-level metadata + broadcast mixing - total and projected list records will be retained at the same time
meegle mywork todo --action done --page-num 1 \
  --select "total,list.work_item_info.work_item_name"
```

### Metadata retention

Under default rendering, the complete structure of the response will be preserved in all `--format`: the `{"list":[...], "total":N, "pagination":{...}}` returned by the List interface is rendered as is - even if you do not name it in `--select`, the `total` / `pagination` metadata fields are still visible. To drill down to a specific record, explicitly use `--select` (with the broadcast syntax above). Under `--format table` and `--format ndjson`, single-key wrappers of the form `{"list":[...]}` (without sibling metadata) will still be losslessly expanded into rows for display.

### Get logid through `--envelope`

When encountering situations such as "returning success but not actually taking effect" or "the output does not meet expectations", you need to let oncall
When checking specific calls by logid, add `--envelope`:

```bash
meegle workflow update-node --work-item-id 12345 \
  --set node_schedule.points=10 --envelope
```

Successful output will be packaged as `{data, meta, error}`, and the backend will hang on when returning logid
In `meta.logid` - give this id to oncall to locate this request in argos.
Logid will be suppressed when `--envelope` is not added, keeping the default output clean for pipeline processing.

AI Handoff's Config, Preference, and Create Link responses will also add the gateway response header `x-tt-logid`
Write `meta.logid`: only displayed after specifying `--envelope` on success, and automatically enter structured error on failure
envelope, no need to enable debug log.

### Dry Run

For commands with side effects, use `--dry-run` to preview the request before executing it:

```bash
meegle workitem create --project-key PROJ --work-item-type story \
--params '{"fields":[{"field_key":"name","field_value":"test"}]}' --dry-run
```

### Command introspection

Use `inspect` to view the complete Parameters information of any command:

```bash
# List all commands
meegle inspect

# View ParametersDetails of a specific command
meegle inspect workitem.create
```

### Programmed command string

When calling via the Go command string SDK embed, you can use `\n` to represent a newline. For example
`--content "First paragraph\n\nSecond paragraph"` will pass two paragraphs to the command. If you need to pass a literal
Character `\n`, please write `\\n`; unsupported escape sequences retain backslashes.

This decoding only works for `CommandClient.Execute`, `ExecuteCommandString` and other programmatic
Command string entry. The `meegle` binary directly receives the Parameters array parsed by the shell, so it still follows
Corresponds to shell quoting and escaping rules.
Command string for Facade remote RPC calls The Go SDK only registers MCP dynamic discovery commands.
Local CLI API commands such as `ai-handoff` and `preference handoff` are only distributed in npm
Provided in the `meegle` CLI; when calling `CallTool` directly, only the MCP tool is still accessed.

## Authentication

### Browser login (default)

```bash
meegle auth login
```

Automatically open the browser to complete OAuth authorization. If the browser is not open, the terminal will display the authorization address, which can be copied manually.

### Device Code login (no browser environment)

```bash
meegle auth login --device-code
```

The terminal displays the QR code and authorization code. Use your mobile phone to scan the code for authorization. Suitable for browser-less environments such as SSH remote servers.

### Other Authentication commands

```bash
# Check the login status (a lightweight tools/list call verification token will be sent to the server, which can be used as
# preflight for cron tasks)
meegle auth status

# Sign out
meegle auth logout
```

The `auth status` exit code is used in conjunction with the `reason` Field to enable cron/CI preflight scripts
Branch processing without parsing human-readable text:

| Exit code | `reason` | Meaning | Recommended action |
|--------|----------|------|----------|
| 0 | — | There is token locally and the server verification passes | Continue execution |
| 1 | `no local token` | No local token | Execute `meegle auth login` |
| 1 | `token rejected by server` | The token has expired or been revoked (refresh cannot restore it) | Execute `meegle auth login` |
| 2 | `server unreachable: <err>` | Network, timeout, 5xx - the call itself failed | Try again later, do not log in again |

JSON output Example (`auth status --format json`), token rejected:

```json
{"authenticated": false, "host": "meegle.com", "reason": "token rejected by server"}
```

For credentials managed by `meegle auth login`, multiple CLI processes sharing the same profile will execute serially
token refresh. An invalid refresh response will not overwrite the original credentials, and a late 401 from the old process will not clear other processes.
The new token has just been refreshed successfully.

## Configuration

### Configuration file

Configuration is stored in `~/.meegle/config.json`:

```bash
#Initialize Configuration
meegle config init

# View the current Configuration
meegle config show

# Modify a single Configuration item
meegle config set host project.feishu.cn

# View a single Configuration item
meegle config get host
```

Main Configuration items:

| Field | Description | Example |
|------|------|------|
| `host` | Site domain name | `project.feishu.cn`, `meegle.com` |
| `user_access_token` | User access token, supports `${VAR}` to be read from environment variables | `${CI_MEEGLE_TOKEN}` |
| `access_token_header` | Customize the HTTP header name that carries the token; leave it blank to use the default `Authorization: Bearer <token>` | `x-meegle-auth` |
| `user_agent` | Appended to the caller section of `User-Agent` (in the form of `meegle-cli/<ver> <user_agent>`); supports `${VAR}` template; overridden by environment variable `MEEGLE_USER_AGENT` | `my-service/1.0` |

### Sandbox/CI: Inject environment variables directly

The CLI will directly read the following conventionally named environment variables without executing any `config set`:

```bash
export MEEGLE_HOST=project.feishu.cn
export MEEGLE_USER_ACCESS_TOKEN=<your-user-token>
export MEEGLE_USER_AGENT=ci-runner # Optional; append to User-Agent, with higher priority than config.user_agent
export MEEGLE_AI_HANDOFF=disabled # Optional; hard disable AI Handoff locally
meegle workitem get --work-item-id 123
```

Each variable can be set independently. When `MEEGLE_USER_ACCESS_TOKEN` is set, the CLI does not access the keychain, and 401 errors are not automatically refreshed and are rotated by the caller. When only setting `MEEGLE_HOST` (without token), the credentials stored in the keychain are still used. `MEEGLE_AI_HANDOFF=disabled` only disables access control locally and does not modify the profile Configuration.

### Custom Auth Header

By default tokens are sent via the standard `Authorization: Bearer <token>`. If the backend requires the token to be placed in another header (and `Authorization` is not allowed), use `access_token_header` to switch:

```bash
meegle config set access_token_header x-meegle-auth
```

Or temporarily override via environment variables:

```bash
export MEEGLE_ACCESS_TOKEN_HEADER=x-meegle-auth
```

When enabled the CLI will send bare values ​​(without the `Bearer` prefix) as `<header>: <token>` and **not send** the `Authorization` header at all - suitable for backends sensitive to the coexistence of both headers.

### Environment variable template

If the environment variable name used by the platform is not `MEEGLE_*`, you can use the `${VAR}` placeholder in config.json to bind any string Field, which will be replaced with the value of the environment variable of the same name at runtime. This not only avoids writing sensitive values ​​in plain text in config.json, but also adapts to the variable naming habits of different containers/CI platforms.

```json
{
  "current": "prod",
  "profiles": {
    "prod":    { "host": "project.feishu.cn", "user_access_token": "${PROD_CI_TOKEN}" },
    "staging": { "host": "staging.feishu.cn", "user_access_token": "${STAGING_CI_TOKEN}" }
  }
}
```

rule:
- Only placeholders for **whole string patterns** are recognized. `"${X}"` will be expanded; `"Bearer ${X}"` will be treated as literal and not expanded.
- When the referenced environment variable is not set or is empty, the business command that requires credentials will fail fast, and the error message will include the field path and variable name; self-rescue portals such as `--help`, `version`, `auth login --help`, `config set`, etc. can still be run to facilitate profile repair.
- When `user_access_token` is configured, it will overwrite the token written by `meegle auth login` in the local keychain. Since there is no local refresh capability in this mode, the server needs to rotate the environment variable values ​​by itself when returning 401.

### Multi-environment Profile

Supports management of multiple environment configurations (different sites, different accounts), and each profile independently stores host and Authentication information.

```bash
# Create new environment (interactive boot host selection + login)
meegle config profile create staging

# View all environments
meegle config profile list

#Switch default environment
meegle config profile use staging

# Check the current environment
meegle config profile current

# Temporarily use other environments (without changing the default)
meegle mywork todo --action this_week --page-num 1 --profile staging

# Delete environment
meegle config profile delete staging
```

## Enterprise CLI extension

Enterprises can rely directly on this Go Module, register trusted in-process adapters and build their own `meegle` binaries without modifying the official repository. V1 provides three extension points:

- `extension/credential` Select the account and provide the existing Meegle user token. Known local/self-rescue commands do not call the Credential Provider on startup, so slow OIDC Providers do not block help, versioning, Configuration fixes, completions, URL parsing, or extended diagnostics; commands that require identity will still resolve the Provider, and any Provider errors will fail-closed.
- `extension/transport` observes or blocks CLI HTTP requests while preserving redirection and TLS anti-downgrade constraints. Provider and Hook callbacks have a 30-second security timeout, but real MCP, OAuth and Attachment requests continue to use the caller Context and the original HTTP Client timeout, so enabling the extension will not truncate large file uploads and downloads or slow server operations; a trusted in-process front Hook will see the real request after the credentials are injected, and technically the Authentication Header can be modified. The CLI does not provide in-process isolation and will not freeze the Header value; the post Hook only receives `Body=http.NoBody`, TLS State has also copied the isolation metadata snapshot, and the blocked Hook will not consume, occupy or modify the real response stream; MCP requests carrying the default Bearer or custom Token Header retain the 10-hop limit and will not be redirected outside the original precise origin.
- `extension/platform` observes, wraps or restricts static commands and MCP dynamic discovery commands. Plug-in metadata/Install and each Startup Hook have a two-second safety boundary; fail-open plug-ins will be skipped after timeout, fail-closed will terminate the CLI, and late registrations will be ignored. The Restrict plug-in must be fail-closed; if the handwritten plug-in declares `Restricts=true` and `FailurePolicy=FailOpen` at the same time, the CLI will fail to start and will not silently skip the policy.

When an enterprise plugin declares `RequireCLI`, the entry should call `cmd.ExecuteWithVersion(version)`; `cmd.Execute()` is still the compatible default entry. The `dev` build will reject version constraints by design, and compatibility errors will prompt you to use `ExecuteWithVersion` instead, which will not bypass the fail-closed check. Extensions are linked at compile time and do not support downloading plug-ins at runtime; the SDK does not load the CLI's global extension registry. For complete usage, see [Extended Example](./examples/README.md) and [Architecture Contract](./docs/design/CLI-EXTENSION-ARCHITECTURE.md).

The extended version binary can view desensitized diagnostic information through `meegle extension doctor|credentials|transport|plugins|policy|discovery`. Credential and Transport diagnostics will distinguish between `not-evaluated`, `active`, and `failed`. Offline diagnosis will not re-call the Provider in order to display the State. Restrict also handles these commands; when troubleshooting needs to be retained, the readonly Allow List should contain `extension/**`.
Policy refuses to follow the explicit structured output format and uses stable error code `CLIENT_COMMAND_DENIED`. Credential and Platform failures that occur before CLI App construction also follow explicit JSON/NDJSON output, exposing `CLIENT_CREDENTIAL_RESOLUTION_FAILED` or `CLIENT_EXTENSION_INSTALL_FAILED` respectively, and will not degrade into plain text.
Error and panic values ​​returned by extended callbacks stay within the boundaries of the protected Go error chain; custom `Is`, `As`, `Unwrap`, `Error` or error payload methods cannot crash the CLI, and panic details do not enter the public output. Extensions still belong to in-process trusted code, and implementers should return ordinary and Context-compliant errors.
All JSON-RPC responses are size-limited before decoding: `tools/list` uses a discovery cap of 8 MiB, other calls use a general cap of 32 MiB per response.
Dynamic discovery supports nullable JSON Schema such as `"type": ["string", "null"]`, and registers Parameters according to its non-null Type in the CLI/SDK. Unions containing multiple non-null Types will be isolated by a single item and output stable `unsupported_schema_union` diagnostics without affecting other tools.

## FAQ

### Command List is empty

When the CLI is started, it will obtain the available command list from the server. If the network is unavailable or you are not logged in, the dynamic command will not be registered. Please make sure you are logged in first:

```bash
meegle auth login
```

The command list will be automatically cached and will be refreshed silently in the background after expiration without affecting use.
When the server command discovery fails and there is no available cache, local commands such as `auth`, `config`, `inspect`, `completion`, `url` and other local commands will still start normally; dynamic business commands will return a `TOOL_DISCOVERY_FAILED` server error until the network is restored.

## Safety and Risk Tips

This tool is designed to be called by AI Agent to automate Meegle Operation, which itself has inherent risks such as model illusion, uncontrollable execution, and prompt word injection. Once you grant Meegle access permissions, the Agent will act as your user within the scope of authorization and may complete high-impact operations such as FieldUpdate, State transfer, and Work ItemCreate. Please fully evaluate before use.

Recommended risk control measures:

- For commands with side effects, use `--dry-run` to preview the request before executing it officially.
- Give Agent a dedicated profile (`meegle config profile create`) to facilitate separate auditing and revocation
- CI or shared environments prefer to use short-term environment variable token (`MEEGLE_USER_ACCESS_TOKEN`), which will rotate automatically when 401 occurs; do not relax the default security settings

By using this tool, you voluntarily assume all related responsibilities.

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=larksuite/meegle-cli&type=Date)](https://star-history.com/#larksuite/meegle-cli&Date)

## contribute

Community contributions are welcome. Please submit bug feedback or feature suggestions to [Issue](https://github.com/larksuite/meegle-cli/issues) or [Pull Request](https://github.com/larksuite/meegle-cli/pulls). For larger changes, it is recommended to discuss them through Issues first.

## License

This project is open source based on **MIT License**.

When the software is running, it will call the API of the Lark/Feishu open platform. The use of these APIs needs to comply with the following agreement and privacy policy:

- [Feishu User Service Agreement](https://www.feishu.cn/terms)
- [Feishu Privacy Policy](https://www.feishu.cn/privacy)
- [Feishu Open Platform Application Service Provider Security Management Specifications](https://open.feishu.cn/document/uAjLw4CM/uMzNwEjLzcDMx4yM3ATM/management-practice/app-service-provider-security-management-specifications)
- [Lark User Terms of Service](https://www.larksuite.com/user-terms-of-service)
- [Lark Privacy Policy](https://www.larksuite.com/privacy-policy)
