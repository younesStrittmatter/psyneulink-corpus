# psyneulink-corpus

Community-curated **brainlike** YAMLs and the canonical issue queue for
human-reported tool feedback for the
[psyneulink-ai](https://github.com/younesStrittmatter) stack.

This repo is **data + review rules**, not code. The MCP server
([psyneulink-mcp](https://github.com/younesStrittmatter/psyneulink-mcp))
reads from this repo at build time (regen) and runtime (brainlike views).
Nothing here imports PsyNeuLink.

## Two contribution flows

1. **File a tool feedback issue** — report problems with a tool exposed
   by `psyneulink-mcp`: misleading description, schema mismatch, missing
   arg, wrong behavior. Use the `tool_feedback` issue template; the 
   `feedback` label is applied automatically. The MCP's generator pulls
   open issues with this label on each regen.
2. **Propose a brainlike entry** — open a PR adding a YAML under
   `community/brainlike/`. The PR template walks you through the schema.
   Maintainers review per `.github/CODEOWNERS`.

A **GitHub account is required** for both. See `docs/CONTRIBUTING.md`.

## Status

Skeleton only. The schema, templates, CODEOWNERS, and CI validation all
land in a follow-up plan.

## Layout

```
community/brainlike/  community-curated YAMLs (one per concept)
users/<gh-handle>/    optional per-user profiles (if you want them shared)
docs/                 contribution guides + schema docs
.github/              issue/PR templates, CODEOWNERS, validation CI
```
