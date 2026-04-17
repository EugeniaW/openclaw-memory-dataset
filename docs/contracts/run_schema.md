# Run Schema

Canonical CSV file: `data/manifests/runs.csv`

## Header

```csv
run_id,study_id,split,task_id,task_variant,agent_name,model_name,memory_mode,memory_backend,environment_id,seed,started_at_utc,ended_at_utc,status,raw_artifact_status,source_path,raw_log_path,trace_path,notes
```

## Field Definitions

| field | required | type | description |
|---|---|---|---|
| `run_id` | yes | string | Unique identifier for the run. |
| `study_id` | yes | string | Study or collection namespace. Use a stable label for this repo or sub-study. |
| `split` | yes | string | Dataset partition label such as `pilot`, `train`, `dev`, or `test`. For the current repo, `pilot` is expected. |
| `task_id` | yes | string | Stable identifier for the task assigned in the run. |
| `task_variant` | no | string | Optional variant or prompt version for the task. |
| `agent_name` | yes | string | Agent or system name that produced the run. |
| `model_name` | no | string | Model identifier when applicable. |
| `memory_mode` | no | string | Memory configuration label, for example `none`, `ephemeral`, or another declared mode. |
| `memory_backend` | no | string | Optional backend or storage implementation label associated with memory for the run. |
| `environment_id` | no | string | Identifier for the execution environment or repo snapshot context. |
| `seed` | no | integer or string | Optional randomness seed or deterministic run label. |
| `started_at_utc` | yes | ISO 8601 UTC datetime | Run start time. |
| `ended_at_utc` | no | ISO 8601 UTC datetime | Run end time, if known. |
| `status` | yes | enum | Final run status: `success`, `error`, `cancelled`, or `incomplete`. |
| `raw_artifact_status` | no | enum-like string | Status of raw artifacts for the run, for example `missing`, `partial`, or `present`. |
| `source_path` | no | path string | Relative path to the source record used to derive this row. |
| `raw_log_path` | no | path string | Relative path to a raw log artifact for the run. |
| `trace_path` | no | path string | Relative path to a richer trace artifact, if any. |
| `notes` | no | string | Free-text curator note. Do not use for findings or analysis. |

## Constraints

- `run_id` must be unique across the file.
- Required columns must be present and non-empty.
- Header order must match the canonical header exactly.
- `split` should be a controlled vocabulary within one dataset release.
- `status` must be one of `success`, `error`, `cancelled`, `incomplete`.
- `started_at_utc` must be valid UTC timestamp text.
- `ended_at_utc`, if present, must be valid UTC timestamp text and not earlier than `started_at_utc`.
- Paths should be repository-relative, not absolute.
- Empty optional values should be encoded as empty CSV cells.

## Example Row

```csv
run-20260415-001,openclaw-memory,pilot,task-example-001,,openclaw,,,,,2026-04-15T00:00:00Z,,incomplete,,,,,
```

This example is illustrative only and does not claim that such a run has been collected.
