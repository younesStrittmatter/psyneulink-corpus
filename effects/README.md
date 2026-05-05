# effects/

PSYCHE **`EffectSpec`** instances — one YAML per empirical pattern a
task is expected to produce. An `EffectSpec` says *what claim about
the data we are testing*, in a form a validator can run against any
`Dataset` produced by the matching `TaskSpec`.

Effects are the formal contract between the empirical literature and
the models that try to reproduce it. `models/<id>/coverage.yaml`
declares, per effect ID in this directory, which ones a model covers,
intentionally omits, or fails — so the catalogue stays honest about
the gap between any given model and the empirical record.

## Source of truth for the schema

The authoritative shape of `EffectSpec` lives as a Pydantic model in
[`psyneulink-psyche`](https://github.com/younesStrittmatter/psyneulink-psyche)
(see issue
[`psyneulink-psyche#1`](https://github.com/younesStrittmatter/psyneulink-psyche/issues/1)).
This README documents the shape so authors can write valid YAML
without Python; if a field name differs between this README and
PSYCHE, **PSYCHE wins** and we update the docs here.

Schema validation against PSYCHE happens from `psyneulink-mcp` once
the `validate_effect` tool lands (Phase 3, t8b). Until then CI only
checks that the YAML parses.

## Field reference

An `EffectSpec` is an object with these fields:

| Field | Required | Type | Notes |
|---|---|---|---|
| `id` | yes | `str` (kebab-case) | Unique within `effects/`. Matches the filename basename. |
| `domain` | yes | `str` | Loose tag, matches the `domain:` of the task this effect belongs to. |
| `task` | yes | `str` | Foreign key to `tasks/<task>.yaml#id`. |
| `description` | yes | `str` | Short paragraph. What the empirical pattern is and why it matters. |
| `citations` | yes | `list[str]` | Foreign keys into `entities/papers/`, kebab-case. May reference papers not yet in the corpus; that's a forward reference, not an error. |
| `test` | yes | `EffectTest` | The actual testable predicate. See below. |

### `EffectTest`

```yaml
test:
  kind: comparison              # comparison | trend | interaction
  group_by: congruency          # name of a TaskSpec factor (base or derived)
  statistic: mean_rt            # mean_rt | mean_accuracy | slope_rt | ...
  expected: "incongruent > congruent"
  tolerance:
    min_effect_ms: 30
```

`kind`:

- `comparison` — the statistic differs between two levels of
  `group_by` in a stated direction (`expected: "A > B"`).
- `trend` — the statistic varies monotonically across an ordered
  factor.
- `interaction` — the comparison contrast is itself modulated by a
  second factor; in that case `group_by` is a list of factor names
  and `expected` is written over the interaction (e.g. one of the
  comparison contrasts is *smaller* than the other).

`group_by` always names a factor that exists on the referenced task —
either a base factor or a `derived_factor`. This is the formal hook
between `tasks/` and `effects/`: an effect can only refer to factors
its task actually defines.

`statistic` is the per-cell quantity computed before applying
`expected`. The vocabulary will firm up as PSYCHE / the MCP grows; for
now common values are `mean_rt`, `mean_accuracy`, and slope/contrast
forms thereof.

`tolerance` is optional; when present it specifies a minimum effect
size for the prediction to count as "matched" (e.g.
`min_effect_ms: 30` for an RT contrast).

## Worked example

```yaml
id: stroop-congruency
domain: stroop
task: stroop
description: >
  Reaction times for incongruent color-word trials are reliably slower
  than for congruent trials in the standard color-naming Stroop.
citations:
  - stroop-1935
  - macleod-1991
test:
  kind: comparison
  group_by: congruency
  statistic: mean_rt
  expected: "incongruent > congruent"
  tolerance:
    min_effect_ms: 30
```

## Conventions

- File basename matches the `id:` field (kebab-case).
- `task:` must match an `id:` of a YAML in `tasks/`. `group_by` must
  name a factor that the referenced task defines.
- Effects this corpus expects models to *fail* on (because they reveal
  a missing mechanism) are first-class citizens; list them honestly
  in `models/<id>/coverage.yaml#fails` or `intentionally_omitted`.
- One effect per file. Closely related claims (e.g. RT congruency vs
  accuracy congruency) get their own files unless they really are the
  same predicate evaluated on a different statistic.
