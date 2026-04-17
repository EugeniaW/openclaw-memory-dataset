# Dataset Specification

## Purpose

This document defines the pilot dataset shape for an OpenClaw memory study.

The repository is intentionally dataset-first:
- define stable objects
- define field contracts
- define validation rules
- avoid analysis or experimental interpretation at this stage

## Design Principles

1. Checkable
Every required field should be machine-checkable with lightweight validation.

2. Minimal
Only fields needed to identify runs, order steps, and join future artifacts are included.

3. Extensible
Optional columns allow later enrichment without breaking the pilot contract.

4. Provenance-first
The dataset should preserve enough source metadata to trace records back to raw logs or exported traces.

## Units Of Data

### Run

A `run` is the top-level unit for one captured OpenClaw execution session.

Examples of what may distinguish runs:
- different tasks
- different prompts
- different models
- different memory settings
- different environments
- repeated executions of the same setup

Run records live in `data/manifests/runs.csv`.

### Step

A `step` is a single ordered event within a run.

A step should be atomic enough to preserve sequence and type, but not so fine-grained that one logical event is split across many rows without need.

Examples of step types:
- `prompt`
- `observation`
- `thought`
- `tool_call`
- `tool_result`
- `memory_read`
- `memory_write`
- `response`
- `error`
- `end`

Step exports should follow the contract in `docs/contracts/step_schema.md`. The file `data/manifests/steps_template.csv` provides the canonical header.

## Directory Conventions

### Required now

- `README.md`
- `docs/dataset-spec.md`
- `docs/contracts/run_schema.md`
- `docs/contracts/step_schema.md`
- `data/manifests/runs.csv`
- `data/manifests/steps_template.csv`

### Expected later

These are intentionally not created yet, but the schemas reserve room for them:
- raw run logs or traces under a future `data/raw/` area
- normalized per-run step files under a future `data/steps/` area
- validation scripts
- analysis notebooks or plots

## Identifier Rules

### `run_id`

- string
- repository-unique
- stable once assigned
- recommended format: `run-YYYYMMDD-XXX`
- example: `run-20260415-001`

### `step_index`

- integer
- starts at `0` within each run
- increments by `1`
- unique within `(run_id, step_index)`

### `step_id`

- optional string convenience identifier
- if present, should be stable and unique within a run
- recommended format: `{run_id}-step-{NNNN}`

## Time Rules

- timestamps should use ISO 8601 UTC
- recommended format: `YYYY-MM-DDTHH:MM:SSZ`
- if sub-second precision is available, use it consistently within a file
- `started_at_utc` and `ended_at_utc` belong to runs
- `timestamp_utc` belongs to steps

## Artifact Linking Rules

The pilot dataset may refer to raw artifacts without storing them yet.

Use relative repository paths when possible:
- `source_path`
- `raw_log_path`
- `trace_path`

If no artifact exists yet, leave the field empty rather than inventing a placeholder path.

## Null / Empty Conventions

For CSV exports:
- use empty string for missing optional values
- do not use textual null markers such as `NULL`, `null`, or `N/A`
- booleans should be lowercase `true` or `false` when present

## Validation Rules

### Run-level

- header must match the documented schema exactly
- `run_id` must be unique
- required fields must be non-empty
- `ended_at_utc` must not be earlier than `started_at_utc` when both are present

### Step-level

- header must match the documented schema exactly
- each row must have a `run_id` present in `runs.csv`
- `(run_id, step_index)` must be unique
- step indices should be contiguous within a run
- `parent_step_index`, when present, must reference an earlier step in the same run
- `ended_at_utc` does not apply to steps; step timing uses `timestamp_utc`

## Non-Goals

This document does not define:
- benchmark metrics
- memory quality labels
- causal interpretations
- statistical tests
- parser or profiler behavior
