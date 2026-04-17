# OpenClaw Memory Study Dataset Pilot

This repository is a dataset-first scaffold for an OpenClaw memory study.

The current goal is to make the dataset layout, field contracts, and manifest templates explicit and checkable before any parser, profiler, plotting, or experiment code is introduced.

## Scope

Included now:
- dataset layout and naming conventions
- run-level manifest contract
- step-level manifest contract
- empty or template CSV manifests for future population

Explicitly not included yet:
- research claims or findings
- data collection code
- parser/profiler implementations
- plotting or analysis code
- experiment protocols beyond what is needed to define the dataset shape

## Repository Layout

```text
.
├── README.md
├── docs
│   ├── dataset-spec.md
│   └── contracts
│       ├── run_schema.md
│       └── step_schema.md
└── data
    └── manifests
        ├── runs.csv
        └── steps_template.csv
```

## Dataset Objects

The pilot dataset defines two primary objects:

1. `run`
A single executed session or trace collection unit. A run captures dataset-level metadata such as environment, task identity, agent/model settings, and links to any raw artifacts.

2. `step`
A single ordered event within a run. A step captures timestamped state transitions or actions, such as prompts, tool calls, observations, memory reads/writes, and terminal outcomes.

The exact required and optional fields are defined in:
- [docs/dataset-spec.md](/home/gina/openclaw-memory-study/docs/dataset-spec.md)
- [docs/contracts/run_schema.md](/home/gina/openclaw-memory-study/docs/contracts/run_schema.md)
- [docs/contracts/step_schema.md](/home/gina/openclaw-memory-study/docs/contracts/step_schema.md)

## Usage

Populate `data/manifests/runs.csv` with one row per run.

For each run, derive step-level records that conform to `docs/contracts/step_schema.md`. The file `data/manifests/steps_template.csv` is a template header for such exports.

## Validation Expectations

A future agent should be able to check the repository with simple rules such as:
- required files exist
- CSV headers match the documented contracts exactly
- `run_id` is unique in `runs.csv`
- `step_index` is contiguous within each `run_id`
- every step row references an existing `run_id`
- timestamps are ISO 8601 UTC strings when present

## Status

Pilot scaffold only. No study results are asserted by this repository.
