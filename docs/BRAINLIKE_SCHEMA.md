# Brainlike schema (`_schema.yaml`)

Every YAML contributed under `community/brainlike/` (except the schema file itself) must validate against `community/brainlike/_schema.yaml`. CI rejects PRs when validation fails.

The schema follows [JSON Schema draft 2020-12](https://json-schema.org/draft/2020-12/schema). This document summarizes each field for authors and reviewers.

## Top-level shape

Brainlike entries are **objects** with required keys `id`, `title`, `description`, `predicates`, and `examples`. **`references`** and **`contributor`** are optional. No other property names are allowed (`additionalProperties: false`).

## Field reference

### `id` (required)

Globally unique under `community/brainlike/` across all contribution files (not only your PR). **`^[a-z0-9-]+$`** (lowercase alphanumeric words separated by hyphens—kebab-case). Matches the basename of `community/brainlike/<id>.yaml`.

### `title` (required)

Short human-readable name, **maximum 80 characters**. Optimized for menus and MCP summaries—not a prose abstract.

### `description` (required)

One paragraph (or tightly related paragraphs) stating what motif you are capturing and why it matters cognitively or computationally. Avoid duplicating predicate bullets verbatim.

### `predicates` (required array, min length **1`)

**What makes this brainlike.** Each array element must be exactly **one falsifiable-ish claim**:

- Prefer testable contrasts (“state mixes prior input proportionally to rate”) over slogans (“neural-like”).
- Split compound statements into separate list items rather than cramming bullets with semicolons.
- Use consistent tense and avoid tool-specific jargon unless the claim truly depends on it.

Bad: *“Acts like cortex.”*

Better: *“Output at each step blends the previous latent state with the current feedforward drive using a convex combination controlled by one scalar rate parameter.”*

### `examples` (required array)

**Concrete PsyNeuLink-oriented snippets, configs, or scenario sketches.** Arrays of strings—you can embed short Python using block scalars (`|`). Aim for runnable or near-runnable excerpts when possible rather than prose-only placeholders.

Ranking (best → acceptable):

1. Executable `psyneulink` construction lines with parameter names spelled as in APIs.
2. Pseudocode spelled like PNL class names (`TransferMechanism`, `IntegrationRate`, projections) tying parameters to predicates.
3. Narrative descriptions of composition—only when a tiny snippet wouldn’t clarify.

Repeat items if you must show contrasting parameter sets—but keep each element self-contained enough for tooling to quote cleanly.

### `references` (optional array)

Pointers to **documentation, issue threads, tutorials, books, DOIs.** Required when predicates rest on non–public-domain neuroscience claims or unpublished architecture notes; encouraged whenever you leaned on PsyNeuLink’s online docs.

### `contributor` (optional string)

GitHub handle (no `@`) of the original contributor, if you want explicit provenance attribution.

## Linting reminders

Before opening a PR:

- Run local validation mirroring CI: `check-jsonschema --schemafile community/brainlike/_schema.yaml community/brainlike/<your-file>.yaml` (see README / CONTRIBUTING workflow).
- Ensure **`id`** is unique under `community/brainlike/`.
- Check **title length** (`<= 80`).
