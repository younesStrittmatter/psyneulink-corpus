# Tool-feedback issue lifecycle

Human tool feedback flows through **`psyneulink-corpus` issues** labeled **`feedback`**. The MCP consumes them at **generator** (`scripts/generate_tools.py`) time in `psyneulink-mcp`, not from the MCP server runtime.

## Filing an issue

- Use the **`Tool feedback`** issue form (**New issue → Tool feedback**) so fields render consistently for the MCP parser.
- Equivalent CLI (adjust `--repo`):  
  `gh issue create --repo younesStrittmatter/psyneulink-corpus --label feedback --template tool_feedback`

The template auto-applies the **`feedback`** label.

## Regeneration pulls open feedback

On each successful tool regeneration run:

1. **`generate_tools.py`** lists **open** issues on the corpus repo with the **`feedback`** label.
2. Issues that **also** have **`consumed`** are **skipped** (they already fed a prior successful regen).
3. Parsed fields (`tool_name`, `issue_type`, `description`, …) merge into prompts for the impacted tool descriptions.

## Success: mark consumed, stay open for humans

When the regenerator succeeds, **`mark_issues_consumed`** (in `psyneulink-mcp`’s corpus helper) performs two actions **per touched issue**:

- Adds a comment: `consumed in regen <sha>` referencing the MCP repo commit/hash used for generation.
- Adds the **`consumed`** label alongside existing labels (including **`feedback`**).

**Issues are not auto-closed.** A maintainer checks that the regenerated tool text or schema actually resolves the complaint, then closes the issue manually—or requests follow-ups if partially addressed.

## After consumption

Issues remain public history. Future regen runs ignore them once **`consumed`** is present, preventing duplicate ingestion while leaving the audit trail intact.

## Summary

```
File (`feedback`) → MCP regen ingest → Comment + `consumed` → Human verify → Close
```

If you rename issue-form field labels or labels themselves, coordinate with `psyneulink-mcp/src/psyneulink_mcp/corpus.py` so parsing does not regress.
