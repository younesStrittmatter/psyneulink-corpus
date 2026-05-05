# entities/people/

One YAML per person referenced elsewhere in the knowledge pool —
authors of papers, theorists associated with brainlike claims,
contributors of models. The point is to have a stable, kebab-cased
identifier (`jonathan-cohen`) we can use as a foreign key from
`entities/papers/`, `models/`, `brainlike/`, etc., without each of
those needing to repeat the person's affiliation and bio.

## Field reference

| Field | Required | Type | Notes |
|---|---|---|---|
| `id` | yes | `str` (kebab-case) | Unique within `entities/people/`. Matches the filename basename. |
| `name` | yes | `str` | Full display name (`"Jonathan D. Cohen"`). |
| `affiliation` | no | `str` | Current primary affiliation, free-form (e.g. `"Princeton University"`). |
| `website` | no | `str` (URL) | Canonical lab / faculty page if there is one. |
| `github_handle` | no | `str` | GitHub username, no `@`. |
| `short_bio` | no | `str` | One paragraph. What this person is best known for in cognitive science / computational modeling. |
| `aliases` | no | `list[str]` | Alternate spellings or initials (helps retrieval / dedup). |

## Worked example

```yaml
id: jonathan-cohen
name: "Jonathan D. Cohen"
affiliation: "Princeton University (Princeton Neuroscience Institute, Department of Psychology)"
website: "https://pni.princeton.edu/faculty/jonathan-cohen"
short_bio: >
  Co-developer of PsyNeuLink and a long-standing voice in
  computational accounts of cognitive control. Best known in this
  corpus for the Cohen, Dunbar & McClelland (1990) PDP account of
  the Stroop effect and the Botvinick et al. (2001) conflict-
  monitoring proposal.
aliases:
  - "Jonathan Cohen"
  - "J. D. Cohen"
```

## Conventions

- File basename matches the `id:` field (kebab-case).
- Only include people the corpus actually references. People graduate
  out of "potentially useful" and into this directory the first time
  another file points at them.
- Keep `short_bio` short. Anything paper-specific belongs on the
  paper, not the person.
