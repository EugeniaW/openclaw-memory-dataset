# Step Schema

Canonical template file: `data/manifests/steps_template.csv`

## Header

```csv
run_id,step_index,step_id,parent_step_index,timestamp_utc,step_type,memory_event_role,actor,tool_name,memory_key,input_ref,output_ref,status,error_code,token_count,latency_ms,source_path,notes
```

## Field Definitions

| field | required | type | description |
|---|---|---|---|
| `run_id` | yes | string | Foreign key to `runs.csv`. |
| `step_index` | yes | integer | Zero-based sequence index within the run. |
| `step_id` | no | string | Optional stable identifier for the step. |
| `parent_step_index` | no | integer | Optional pointer to an earlier step in the same run. |
| `timestamp_utc` | yes | ISO 8601 UTC datetime | Timestamp for when the step occurred. |
| `step_type` | yes | enum-like string | Event type such as `prompt`, `observation`, `thought`, `tool_call`, `tool_result`, `memory_read`, `memory_write`, `response`, `error`, or `end`. |
| `memory_event_role` | no | string | Optional role for memory-related steps, for example `read`, `write`, `lookup`, or `evict`. Leave empty for non-memory steps. |
| `actor` | no | string | Actor responsible for the step, for example `user`, `assistant`, `system`, or a tool name. |
| `tool_name` | no | string | Tool identifier when the step concerns a tool interaction. |
| `memory_key` | no | string | Memory item or namespace referenced by the step, when applicable. |
| `input_ref` | no | string | Reference to an input payload, message id, or artifact fragment. |
| `output_ref` | no | string | Reference to an output payload, message id, or artifact fragment. |
| `status` | no | enum-like string | Optional step status such as `ok` or `error`. |
| `error_code` | no | string | Error label or code when a step fails. |
| `token_count` | no | integer | Optional token count associated with the step. |
| `latency_ms` | no | integer | Optional latency in milliseconds. |
| `source_path` | no | path string | Relative path to the raw artifact from which this step row was derived. |
| `notes` | no | string | Free-text curator note. Do not use for analysis claims. |

## Constraints

- Header order must match the canonical header exactly.
- `(run_id, step_index)` must be unique.
- `step_index` starts at `0` and should increase contiguously by `1` within each run.
- `parent_step_index`, if present, must be less than `step_index`.
- `timestamp_utc` must be valid UTC timestamp text.
- `run_id` must already exist in `data/manifests/runs.csv`.
- Paths should be repository-relative, not absolute.
- Empty optional values should be encoded as empty CSV cells.

## Notes On Granularity

The pilot schema is intentionally permissive about `step_type` so later agents can normalize different source traces into one contract without prematurely fixing a parser implementation.

If future collection reveals the need for tighter enums or separate event tables, that should be a schema revision rather than an ad hoc CSV change.

## Example Row

```csv
run-20260415-001,0,run-20260415-001-step-0000,,2026-04-15T00:00:01Z,prompt,,user,,,,,,,,,
```

The example is illustrative only and does not claim that such a step exists.
