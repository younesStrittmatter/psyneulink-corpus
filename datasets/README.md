# datasets/

Behavioral (and later eye-tracking, EEG, fMRI) datasets, **plus the
`TaskSpec` instance that produced them** so any study can be re-run
or extended on its own terms.

A dataset directory is identified by a `domain` and a `study-id` and
holds three things at minimum:

```
datasets/<domain>/<study-id>/
  data.csv              # PSYCHE-Convention-conformant table (one row = one observation)
  task.yaml             # PSYCHE TaskSpec snapshot used to generate / collect this dataset
  provenance.yaml       # who, when, how (sim vs human), reproducibility metadata
```

This is *not* an aggregate behavioral data lake. Each study lives in
its own directory so a model that targets a specific paper can pull
exactly that study's data and the task spec that goes with it,
without disambiguation.

## Source of truth for the dataset shape

The authoritative shape of a `Dataset` lives as `DatasetSpec` in
[`psyneulink-psyche`](https://github.com/younesStrittmatter/psyneulink-psyche)
(see issue
[`psyneulink-psyche#1`](https://github.com/younesStrittmatter/psyneulink-psyche/issues/1)),
which extends the existing `BEHAVIORAL_DATA_CONVENTION`. `data.csv`
columns must conform to the convention referenced by the task's
`measures[*].convention` (initially `behavioral`, later
`eye_tracking`, `eeg`, `fmri`).

Schema validation against PSYCHE happens from `psyneulink-mcp` once
the `find_dataset` / `validate_effect` tools land (Phase 3, t8 / t8b).
Until then CI only checks that the YAML and CSV parse.

## Layout

```
datasets/
  <domain>/                       # loose grouping tag, matches tasks/effects/models
    <study-id>/                   # kebab-case unique identifier for the study
      data.csv                    # behavioral observation table
      task.yaml                   # TaskSpec snapshot (often a copy / pin of tasks/<task>.yaml)
      provenance.yaml             # see field reference below
```

`<study-id>` convention: `<first-author-lastname>-<year>-<short-tag>`
(e.g. `cohen-1990-stroop`), or `<source>-<short-tag>` for synthetic
data (e.g. `pnl-sim-stroop-v1`). Reuses or mirrors entity-paper IDs
when a study corresponds 1:1 to a published paper.

## `provenance.yaml` reference

```yaml
study_id: cohen-1990-stroop
domain: stroop
task: stroop                       # FK to tasks/stroop.yaml#id
source: human                      # human | simulated | mixed
collected_at: "2025-XX-XX"         # ISO date or YYYY range when the data was collected
contributors:                      # FK list into entities/people/
  - jonathan-cohen
paper:                             # optional; FK to entities/papers/ if the dataset
                                   # corresponds to a published study
  - cohen-1990-stroop
notes: >
  Free-form description of how the data was obtained: which sub-
  experiment of the paper, which subjects, any exclusions applied
  prior to landing here.
```

| Field | Required | Notes |
|---|---|---|
| `study_id` | yes | Matches the `<study-id>` directory name. |
| `domain` | yes | Matches the `<domain>` parent directory. |
| `task` | yes | FK to `tasks/<task>.yaml#id`. |
| `source` | yes | `human` (real subjects), `simulated` (model-generated), `mixed` (e.g. cleaned human plus model-imputed missing trials). |
| `collected_at` | yes | ISO date or YYYY/year-range string. For simulated data, the date the simulation was run. |
| `contributors` | yes | FK list into `entities/people/`. |
| `paper` | no | FK list into `entities/papers/` when the dataset corresponds to a published study. |
| `notes` | no | Short paragraph. |

## What's seeded today

The directory shape is documented; **no real datasets exist yet**. The
first dataset will land when the SweetBean integration in
`experiment-mcp` (Phase 2 / Phase 3) starts producing
`DatasetSpec`-compliant output, and the first simulated dataset will
land alongside the first PNL composition for
`models/cohen-1990-stroop-simple/`. Until then `datasets/stroop/`
holds a placeholder `README.md` so the directory exists and the shape
is browsable.

## Conventions

- One study per `<study-id>` directory; no nested studies.
- `data.csv` rows obey `BEHAVIORAL_ROW_IDENTITY`: one row = one
  behavioral observation; `(subject_id, trial_global, step)` is the
  unique identifier.
- The `task.yaml` snapshot is a *copy* of the relevant
  `tasks/<task>.yaml` at the time the data was generated. Do not
  rewrite it after the fact, even if the canonical `tasks/<task>.yaml`
  evolves — the snapshot is provenance.
- Don't put real human PII (names, MRNs, raw biometrics) into the
  dataset. Subject identifiers are pseudonymized opaque strings.
