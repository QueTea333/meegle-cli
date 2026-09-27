# CLI usage guide

## Preconditions

The running environment requires Node.js 18+. All commands are executed through `meegle`.

## Command structure

```bash
meegle <resource> <method> [flags] --format json
```

The command adopts `resource method` two-level structure. For all output, it is recommended to use `--format json` to obtain structured data.

## Global Flag

| Flag | Description |
|------|------|
| `--format json\|table\|ndjson` | Output format, default json |
| `--select <props>` | Select output properties, comma separated (support dot path, such as `name,owner.name`) |
| `--profile <name>` | Temporarily switch profile |
| `--verbose` | Show detailed logs |
| `--refresh` | Refresh the local command cache from the server (bypass 24h cache) |

## Parameter passing

Several ways, priority from high to low:

1. **Flag mode** (recommended): `--project-key PROJ --work-item-type story`
2. **--fields mode** (write work item fields, repeatable): `--fields '{"field_key":"name","field_value":"Task title"}' --fields '{"field_key":"priority","field_value":"1"}'`
3. **--params mode** (complete JSON summary): `--params '{"fields":[{"field_key":"name","field_value":"Task title"}]}'`
4. **--set mode** (only top-level parameter shortcut, fields[] is not supported): `--set page_num=1` is equivalent to `--page-num 1`, supports dot-path nesting; do not use it to write work item fields

Flag overrides `--params`; `--set` only affects top-level parameters and will not be written to `fields[]`.

**`field_value` filling rules**: fixed filling string. Scalars are filled with strings directly, such as `"100"`, `"true"`; arrays or objects are filled with serialized JSON strings, such as `"[\"u1\",\"u2\"]"`, `"{\"type\":\"auto\"}"`. Do not pass raw number, array or object to `field_value`.

This restriction only applies to `field_value`; parameters such as `fields`, `role_operate`, `node_schedule` still pass native arrays or objects according to their respective schema.

## Command discovery

CLI commands and parameters will be updated with versions. When encountering an uncertain command or parameter, use `inspect` to get the latest information:

```bash
meegle inspect # List all available commands
meegle inspect workitem.create # View the parameter schema of a specific command
```

> Command list is cached locally for 24 hours. If the parameters output by `inspect` do not match the actual server, or the server has a new command but the CLI reports `unknown command`, add `--refresh` to force the latest list to be re-pulled from the server:
> ```bash
> meegle --refresh inspect workitem.create
> ```

## Output processing

- Always use `--format json` to get structured output for easy parsing
- Use `--select` to simplify the returned fields, such as `--select id,name,current_nodes.name`
- When the command returns an error, the JSON contains the `error` and `message` fields
