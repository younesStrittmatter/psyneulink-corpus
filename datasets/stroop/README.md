# datasets/stroop/

Placeholder. **No real Stroop datasets are seeded yet.**

The first dataset under this directory will land in a later phase
when one of the following starts producing PSYCHE-`DatasetSpec`-
compliant output:

- `experiment-mcp` (Phase 2 / Phase 3) — SweetBean / SweetPea runs of
  `tasks/stroop.yaml`. Expected first study ID:
  `<sweetbean-pilot-stroop-vN>` with `source: human` (or `simulated`
  if SweetBean's own simulator is used).
- `models/cohen-1990-stroop-simple/` (later in Phase 1 / Phase 2) —
  PNL composition runs that produce a simulated Stroop dataset.
  Expected first study ID: `pnl-sim-stroop-v1` with `source:
  simulated`.

When either lands, the directory layout becomes:

```
datasets/stroop/<study-id>/
  data.csv
  task.yaml          # snapshot of tasks/stroop.yaml at run time
  provenance.yaml
```

See `datasets/README.md` for the full shape.
