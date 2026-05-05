# models/

PsyNeuLink models, one directory per model. Each directory carries
the metadata that lets the rest of the stack reason about the model
*as an entry in the catalogue* — what it claims, what it doesn't, and
where the executable build will eventually live.

A model directory contains, at minimum:

```
models/<model-id>/
  model.yaml         # metadata: id, domain, framework, paper FK, status, …
  coverage.yaml      # honest coverage matrix vs effects/ in this domain
  paper.yaml         # citation block; foreign-keys into entities/papers/
```

Later phases will add the executable PsyNeuLink composition itself
(e.g. a `composition.yaml` or `composition.py`) plus simulation
artifacts under `runs/`. For now models are metadata-only seeds — the
executable build path is captured as a TODO field on `model.yaml`.

## Honest coverage is a hard rule

`coverage.yaml` is the load-bearing file. Every model must declare,
for the effects in its domain, which it covers, which it
intentionally omits (with a one-line "why not"), and which it fails.
A model that covers everything in its domain and a model that covers
two-of-six effects are equally welcome — but both must be honest
about it.

The point is to make the gap between any given model and the
empirical record visible at retrieval time, so the agent never
silently treats a model as a complete account of its domain.

## `model.yaml` reference

```yaml
id: cohen-1990-stroop-simple
domain: stroop
framework: psyneulink
paper: cohen-1990-stroop          # FK to entities/papers/cohen-1990-stroop.yaml
status: exemplar                  # exemplar | wip | deprecated | reference-only
description: >
  Brief paragraph: which slice of the paper this build is, what it's
  meant to demonstrate, and any caveats.
composition:
  path: TODO                      # populated when the PNL build lands
  format: TODO                    # composition.yaml | composition.py
```

| Field | Required | Notes |
|---|---|---|
| `id` | yes | kebab-case, matches the directory name. |
| `domain` | yes | Loose tag, matches the domain of the effects this model targets. |
| `framework` | yes | `psyneulink` for now; held generic so other frameworks can be catalogued without breaking the schema. |
| `paper` | yes | FK to `entities/papers/`. |
| `status` | yes | `exemplar` (canonical, recommended starting point), `wip`, `deprecated`, or `reference-only`. |
| `description` | yes | Short paragraph. |
| `composition.path` | yes | Relative path to the executable build inside the model directory. **OK to use `TODO`** while the build hasn't landed; explain why in `description`. |
| `composition.format` | yes | The format at `composition.path` (`composition.yaml`, `composition.py`, …). Also `TODO` until the build lands. |

## `coverage.yaml` reference

```yaml
model: cohen-1990-stroop-simple
domain: stroop
covers:
  - stroop-congruency
  - stroop-word-dominance
intentionally_omitted:
  - stroop-sequential: "no trial-history mechanism in this model — would need a control loop or activation carry-over"
fails: []
```

| Field | Required | Notes |
|---|---|---|
| `model` | yes | Matches the parent `model.yaml#id`. |
| `domain` | yes | Matches the parent `model.yaml#domain`. |
| `covers` | yes | List of `effects/<id>` IDs this model is expected to reproduce. May be empty if the model is intentionally narrow. |
| `intentionally_omitted` | yes | List of `{effect-id: "one-line why not"}` entries. May be empty. Use this when the omission is a *design choice*, not a bug. |
| `fails` | yes | List of `effects/<id>` IDs the model has been seen to fail on. May be empty. Use this when the omission is honestly a known shortcoming, not a design choice. |

## `paper.yaml` reference

A small redundant pointer to the canonical paper for this model.
The full bib entry lives in `entities/papers/<paper-id>.yaml`; this
file mirrors the FK so a reader inside the model directory has the
citation in hand without crossing directories.

```yaml
id: cohen-1990-stroop
title: "On the control of automatic processes: a parallel distributed processing account of the Stroop effect."
authors:
  - jonathan-cohen
  - kevin-dunbar
  - james-l-mcclelland
year: 1990
journal: "Psychological Review"
doi: "10.1037/0033-295X.97.3.332"
entity: cohen-1990-stroop          # FK to entities/papers/cohen-1990-stroop.yaml
```

## Worked example

The canonical seed is `models/cohen-1990-stroop-simple/`. It
deliberately covers the two Stroop effects a feed-forward Cohen-style
network produces and intentionally omits the Gratton sequential
effect — the model has no trial-history mechanism, so adding the
sequential effect to `covers:` would be dishonest.

## Conventions

- Directory name matches `model.yaml#id` (kebab-case).
- `coverage.yaml` is required, even if everything is empty —
  silence is not honesty.
- The `intentionally_omitted` and `fails` distinction matters:
  *omitted* is a deliberate scope choice; *fails* is "we tried, it
  didn't reproduce". Don't paper over the second as the first.
- Don't add executable Python here. The catalogue stays Python-free;
  PsyNeuLink composition files (when they land) are still data, just
  in a `.py` form that PSYCHE / the MCP knows how to load.
