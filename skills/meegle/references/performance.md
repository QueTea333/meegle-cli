# Performance and concurrent call guide

This document contains engineering rules for reducing delays. The core protocol (field formatting, error healing) is still in the main SKILL.md file.

## Parallel calls

Command calls without dependencies should be initiated in parallel, while those with dependencies must be initiated serially.

**Must be serial** (the output of the former is the input of the latter):
- `project search` → `workitem meta-fields` → `workitem query`
- `workitem get` → `workflow transition` / `workitem update`
- `workitem meta-fields` → `workitem create`

**Parallelizable**:
- `workitem meta-fields` and `workitem meta-roles` (same type)
- `workitem meta-fields` for multiple work item types (such as story + issue)
- Count query for each condition, multi-person scheduling batch query

## Large result processing

- **Query in batches**: `workhour list-schedule` split into batches when there are multiple people ≤ 20 people in parallel
- **Simplified SELECT**: Select only necessary fields and avoid large fields such as rich text
- **Page on demand**: First read the homepage to get `list[0].count`; `workitem query` uses `--session-id` + `--group-pagination-list` to get the full amount page by page (SKILL.md "page to get the full amount"), do not use `LIMIT offset, n`
