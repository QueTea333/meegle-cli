# AI assistant formal processing details

This file is used to determine whether to provide an AI assistant link after the Meegle CLI has completed the executable part, or it is clear that it cannot execute the user's request. Links are supplementary entries and do not replace CLI results.

## Scope of application

| Scenario | Example | What the CLI does first | Continue processing target |
| --- | --- | --- | --- |
| Analysis, tracing, summary, diagnosis, and judgment | "Why are these requirements stuck?" | Query status distribution and operation records | Analyze common causes, risks and suggestions |
| Scheduling or working time analysis | "Is our group overloaded this week?" | If the query is possible, schedule details will be returned first | Analyze load and conflicts |
| Tracing historical changes or delays | "Why did this requirement take so long?" | Query operation records | Summarize key changes and reasons for delays |
| Weekly report or to-do summary | "Write a weekly report for me" | Query to-do and necessary operation records | Generate structured weekly report |
| Work item operations not supported by CLI | "Freeze these requirements" | Make it clear that CLI cannot currently execute | Let the AI ​​assistant continue to complete the operation |
| Generate or modify a metric chart | "Add a dimension to this chart" | Return to the status quo if it can be queried | Let the AI ​​assistant continue to modify the chart |

No links are provided in the following situations: pure data query, export or enumeration without analysis intention; single instance detailed view; user explicitly only wants data; user has refused; this session has been actively guided once.

## Processing process

1. **Complete work within the CLI scope first**: When there is an executable query or operation, the real results are returned first. When the request is not supported by the CLI at all, explain the capability boundaries before continuing to judge, and do not forge queries to satisfy the process.
2. **Check availability**: Call `ai-handoff availability`. Stop when `available=false`; if the original request can only continue to be processed, a brief explanation of the entry is currently unavailable. Successful results are cached by profile for about one hour, and preflight is not repeated for the same session; the final creation is still re-verified by the server.
3. **Compliance Pattern**:
- `auto`: continue to generate links when it meets the applicable scope;
- `ask`: Ask the user first if they want to continue in the AI ​​assistant. `ai-handoff create-link` must not be called before explicit consent is obtained; after consent, it can be created directly without repeated pre-check;
- `off`: Stop and do not prompt the user to modify preferences.
4. **Construct query**: Only describe the incremental tasks that the AI ​​assistant will continue to complete, and follow the rules below.
5. **Construct the associated context**: Only pass the ID pointer required to restore the business object, follow the type and downgrade strategy below.
6. **Create link**: Call `ai-handoff create-link`. Only when `available=true` is returned and contains `url`, the link is attached; when `available=false` is returned, the stop signal is processed. Do not hard-code the domain name, the CLI will normalize the host according to the current login environment.
7. **Attach to the end of the reply**: Explain what the link can continue to accomplish, for example: `If you need to continue analyzing the cause of the stuck point and processing suggestions, you can continue in the AI ​​assistant: <url>`. MUST NOT imply that the CLI query result set was passed in completely.

After the user refuses, this session will no longer prompt. When the user explicitly requests persistent shutdown or reopening, `preference handoff off` or `preference handoff auto` are called respectively; `preference handoff ask` is called only when the user explicitly requests the "ask first" mode.

## query construction rules

Queries are written in natural language that users can directly understand, without exposing CLI, MQL, commands or internal orchestration processes. Naturally includes:

- Continue processing targets;
- Object scope and filtering semantics, such as space, work item type, status, responsible person, and time range;
- Summary of observed phenomena or current status;
- The analysis, recommendation, or action you want the AI ​​assistant to proceed with.

The correlation context only provides the ID pointer and does not contain the full CLI result. When the context granularity is insufficient, the query must supplement the "scope fingerprint": object type, key status, responsible person, time range and common characteristics. Do not write a complete list of objects, and do not treat the number of paginated results as a true total.

Example:

```text
Analyze the requirements in the Meego space that are in the processing status and have been updated this week, focus on determining which progress is lagging behind, which link is stuck, and what risks there are, and give priority attention and processing suggestions.
```

## Associated context strategy

Select payloads according to the following priorities. The current continuation processing strategy does not use type=2 (WorkItemType).

| priority | type | name | payload | applicable scenarios |
| ---: | ---: | --- | --- | --- |
| 1 | 4 | View | `view.project_key`, `view.view_id`, optional `view.work_item_type_key` | User provides view link, range restoration is the most stable |
| 2 | 3 | WorkItem | `work_item.project_key`, `work_item.work_item_type_key`, `work_item.work_item_id` | A small number of clear work items, no more than 3 by default |
| 3 | 1 | Project | `project.project_key` | Batch results, cross-type or inability to pass finer-grained objects |
| — | 5 | MeasureChart | `measure_chart.project_key`, `measure_chart.chart_id` | Measure chart scene |

- When the user provides a view or work item URL, call `url decode` first to extract the ID from the result; do not manually split the URL. Even if subsequent queries fail due to permissions, the resolved ID can still be used to express user intent.
- type=3 is only used for a small number of objects where the user directly gives a URL or an explicit anchor. When there are more than 3 items, it degenerates to type=1, and the range fingerprint is supplemented in the query. The `limits.max_related_context_items` returned by the interface is only the upper limit of the protocol, and it is not recommended to fill it up one by one.
- `--related-context` accepts a strict JSON object or array. When a parsing error occurs, the JSON quotes, field names, or type/payload correspondences are corrected only once; such local parameter corrections are not counted as link creation retries.

Work item example:

```bash
meegle ai-handoff create-link --query 'Analyze the reason for the delay of this work item and give next step suggestions' --related-context '{"type":3,"work_item":{"project_key":"space_key","work_item_type_key":"story","work_item_id":"123456"}}' --format json
```

## Failure and retry

- `available=false`, `mode=off`, `HANDOFF_REJECTED` or `LOCAL_DISABLED` are clear stop signals, do not retry, and do not recommend users to bypass the policy.
- `ai-handoff create-link` has built-in transport layer retries. Do not call again from Skill after any creation failure to avoid repeated link generation; just briefly explain to the user that the link is temporarily unavailable.
- A maximum of one link is generated per round, and the same session can be actively guided at most once. When the user subsequently explicitly requests a regeneration, it is considered a new explicit request.
