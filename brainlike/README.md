# brainlike/

**Theoretical claims about how brains work.** Not models. Not effects.
A `brainlike` entry is a falsifiable-ish proposition that one or more
papers have argued is true of biological computation, expressed at a
level of abstraction that PsyNeuLink-style models can take a stance
on.

The retrieval layer treats `brainlike/` entries as cite-attributed
prior knowledge that the agent can pull into its scratchpad when a
goal mentions a related domain or mechanism. They're not predicates
the validator runs against data — that's what `effects/` is for.

## Relationship to the legacy `community/brainlike/` directory

The existing `community/brainlike/` directory in this repo holds the
PsyNeuLink-flavored "motif" YAMLs (`TransferMechanism leaky integrator`,
…) and stays exactly where it is. Top-level `brainlike/` is the new
home for *theoretical claims about brains* — a strictly broader
notion that is not tied to PsyNeuLink primitives. Existing community
content can migrate later if it fits this shape; that's a separate
scope.

For now, treat the two directories as parallel:

- `community/brainlike/` — community-curated PNL motifs validated
  against `community/brainlike/_schema.yaml`. CI enforces.
- `brainlike/` — claims about brains, cite-attributed, schema
  documented in this README. CI only checks that the YAML parses
  until the schema firms up.

## Field reference

A `brainlike` entry is an object with these fields:

| Field | Required | Type | Notes |
|---|---|---|---|
| `id` | yes | `str` (kebab-case) | Unique within `brainlike/`. Matches the filename basename. |
| `domain` | yes | `str` | Loose tag, open vocabulary (e.g. `stroop`, `cognitive-control`, `working-memory`). |
| `title` | yes | `str` | One-line human-readable name. |
| `claim` | yes | `str` | One short paragraph stating the theoretical claim. Should be falsifiable in principle. |
| `predicates` | yes | `list[str]` | Bullet-form refinements of the claim, one falsifiable-ish proposition per item. |
| `mechanisms` | no | `list[str]` | Computational mechanisms the claim is typically associated with (e.g. "lateral inhibition", "phasic dopamine"). Free-form. |
| `citations` | yes | `list[str]` | FK list into `entities/papers/`, kebab-case. May reference papers not yet in the corpus; forward references are fine. |
| `attributed_to` | no | `list[str]` | FK list into `entities/people/`. Use when the claim is closely associated with named theorists. |

## Worked example

```yaml
id: conflict-monitoring-acc
domain: cognitive-control
title: "ACC implements conflict monitoring"
claim: >
  Anterior cingulate cortex (ACC) computes a scalar conflict signal
  proportional to simultaneous activation of competing response
  representations, and that signal drives downstream upregulation of
  cognitive control on subsequent trials.
predicates:
  - "Conflict (Hopfield-style energy / co-activation of competing responses) increases ACC activity."
  - "Trial-to-trial control adjustment scales with prior-trial conflict."
  - "The Gratton congruency-sequence effect is a behavioral signature of this loop."
mechanisms:
  - "co-activation-based conflict signal"
  - "control-loop feedback to upstream task units"
citations:
  - botvinick-2001
  - macleod-1991
attributed_to:
  - jonathan-cohen
```

## Conventions

- File basename matches the `id:` field (kebab-case).
- One claim per file. If a paper makes multiple distinct claims, they
  go in separate files that all cite the same paper.
- Keep claims at the level *brains in general*, not *one specific
  PNL composition*. Implementation-flavored content belongs in
  `community/brainlike/` (PNL motifs) or `models/`.
