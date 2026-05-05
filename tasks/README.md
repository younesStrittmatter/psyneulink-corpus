# tasks/

PSYCHE **`TaskSpec`** instances — one YAML per cognitive task. A
`TaskSpec` describes *what to run, and how*: the experimental factors,
any factors derived from them, the trial-level structure, and the
behavioral measures the task produces.

Tasks here are the formal glue between the empirical literature
(`effects/`, `entities/papers/`), the models that try to explain it
(`models/`), and the data that comes out of running it (`datasets/`).
Anything that wants to talk about a task *by name* should refer to a
file in this directory by its `id:`.

## Source of truth for the schema

The authoritative shape of `TaskSpec` lives as a Pydantic model in
[`psyneulink-psyche`](https://github.com/younesStrittmatter/psyneulink-psyche)
(see issue
[`psyneulink-psyche#1`](https://github.com/younesStrittmatter/psyneulink-psyche/issues/1)).
This README documents the shape so authors can write valid YAML
without having Python installed; if a field name differs between this
README and PSYCHE, **PSYCHE wins** and we update the docs here.

Schema validation against PSYCHE happens from `psyneulink-mcp` once
those tools land. Until then CI only checks that the YAML parses.

## Field reference

A `TaskSpec` is an object with these fields:

| Field | Required | Type | Notes |
|---|---|---|---|
| `id` | yes | `str` (kebab-case) | Unique within `tasks/`. Matches the filename basename. |
| `domain` | yes | `str` | Loose tag, open vocabulary (e.g. `stroop`, `nback`). Used for grouping in retrieval. |
| `description` | yes | `str` | One short paragraph. What the task is, why it exists in the literature. |
| `factors` | yes | `list[Factor]` | Experimental factors manipulated within the task. |
| `derived_factors` | no | `list[DerivedFactor]` | Factors computed from base factors at trial time (e.g. `congruency`). |
| `trial_structure` | yes | `TrialStructure` | Block / trial-count / randomization layout. |
| `measures` | yes | `list[Measure]` | What the task records per row. Each `Measure` carries a `convention` tag so non-behavioral measures (eye-tracking, EEG) can be added later without breaking the schema. |

### `Factor`

```yaml
- name: color
  levels: [red, blue, green]
```

### `DerivedFactor`

A factor whose value is computed from base factors. Each `level` has a
boolean `predicate` (a stringified expression over the base-factor
names listed in `over`).

```yaml
- name: congruency
  kind: within_trial            # within_trial | across_trial | across_block
  over: [color, word]
  levels:
    - name: congruent
      predicate: "color == word"
    - name: incongruent
      predicate: "color != word"
```

`kind` declares the temporal scope of the predicate:

- `within_trial` — predicate is evaluated using factor values from a
  single trial.
- `across_trial` — predicate spans the current trial plus prior
  trial(s); useful for sequential / Gratton-style derived factors.
- `across_block` — predicate spans block-level structure; useful for
  proportion-congruent manipulations.

### `TrialStructure`

```yaml
trial_structure:
  blocks: 4
  trials_per_block: 36
  randomization: balanced_latin_square     # latin_square | balanced_latin_square | random | fixed
```

### `Measure`

Each row of a task's resulting dataset has columns drawn from these
measures. The `convention` tag lets `DatasetSpec` route the column to
the right validator (`behavioral`, later `eye_tracking`, `eeg`,
`fmri`).

```yaml
measures:
  - name: reaction_time
    convention: behavioral
    column: reaction_time
  - name: response
    convention: behavioral
    column: response
  - name: correct
    convention: behavioral
    column: correct
```

## Worked example

The canonical Stroop seed in this repo is `stroop.yaml`. Read it
alongside the matching effect (`effects/stroop-congruency.yaml`) and
the model coverage file
(`models/cohen-1990-stroop-simple/coverage.yaml`) to see the full
end-to-end shape.

## Conventions

- File basename matches the `id:` field (kebab-case).
- One task per file. Variants (e.g. reverse-Stroop) get their own
  file with their own `id`.
- Don't embed effect logic here. Effects live in `effects/` and refer
  back to the task by `task: <task-id>`.
- The `domain:` tag is a loose grouping tag, not a controlled
  vocabulary — pick a sensible string and reuse it across related
  tasks/effects/models/datasets.
