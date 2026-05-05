# entities/papers/

One YAML per paper referenced elsewhere in the knowledge pool. Bib-
style metadata: title, authors, year, journal, DOI, optional URL. The
point is to have a stable, kebab-cased identifier (`cohen-1990-stroop`)
we can use as a foreign key from `effects/<id>.yaml#citations`,
`models/<id>/paper.yaml`, `brainlike/<id>.yaml#citations`, etc.

`authors` is a list of foreign keys into `entities/people/`. If an
author isn't yet in the corpus, you can leave them as a string
placeholder (`"unknown"`) or wait to add the paper until that author's
people-entity is in place — but **prefer to add the missing person**
so retrieval has a stable handle.

## Field reference

| Field | Required | Type | Notes |
|---|---|---|---|
| `id` | yes | `str` (kebab-case) | Unique within `entities/papers/`. Matches the filename basename. Convention: `<first-author-lastname>-<year>-<keyword>`. |
| `title` | yes | `str` | Full paper title. |
| `authors` | yes | `list[str]` | FK list into `entities/people/`. Order = author order. |
| `year` | yes | `int` | Publication year. |
| `journal` | yes | `str` | Journal / venue name. Use `"preprint"` if not yet in a venue and `"book"` for chapters. |
| `doi` | no | `str` | DOI without the `https://doi.org/` prefix (e.g. `"10.1037/0033-295X.97.3.332"`). |
| `url` | no | `str` (URL) | Canonical link, only if no DOI is available. |
| `volume` | no | `str` | Journal volume. |
| `pages` | no | `str` | Page range, e.g. `"332-361"`. |
| `abstract` | no | `str` | Short paragraph. Optional but helpful for retrieval. |

## Worked example

```yaml
id: cohen-1990-stroop
title: "On the control of automatic processes: a parallel distributed processing account of the Stroop effect."
authors:
  - jonathan-cohen
  - kevin-dunbar
  - james-l-mcclelland
year: 1990
journal: "Psychological Review"
volume: "97"
pages: "332-361"
doi: "10.1037/0033-295X.97.3.332"
abstract: >
  PDP account of the Stroop effect: a feed-forward network with
  competing color-naming and word-reading pathways and top-down
  task-demand modulation reproduces both the canonical congruency
  interference and the asymmetry between the two pathways.
```

## Conventions

- File basename matches the `id:` field (kebab-case).
- Author entries are FKs into `entities/people/`. Add the missing
  people-entity in the same PR rather than leaving dangling FKs.
- One paper per file. If a chapter and book have separately citable
  DOIs, that's two files.
