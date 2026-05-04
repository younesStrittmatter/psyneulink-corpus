# Contributing to psyneulink-corpus

This repository holds community **brainlike** YAML definitions and the canonical queue for human-reported **tool feedback** for the [`psyneulink-mcp`](https://github.com/younesStrittmatter/psyneulink-mcp) server. Everything here is data and review metadata only—there is no Python package in this repo.

You need a **GitHub account** for either flow below.

---

## File a tool feedback issue

Report problems with a tool exposed by `psyneulink-mcp`: misleading description, JSON/schema mismatch, missing argument, incorrect behavior, or similar.

1. Open the repo on GitHub and choose **Issues** → **New issue**.
2. Select the **Tool feedback** template.
3. Fill in every required field (`Tool name`, `Issue type`, `Description`; optional fields are welcome).
4. Submit. The **`feedback`** label is applied automatically.

The MCP’s generator (`psyneulink-mcp` / `scripts/generate_tools.py`) pulls **open** issues that have the **`feedback`** label into the tool-regeneration prompts. After a successful regen, it comments with `consumed in regen <sha>` and adds the **`consumed`** label so the issue can be audited. Maintainers verify the fix in generated tools and close the issue when appropriate. Already-**`consumed`** issues are skipped on later regens.

For command-line workflows you can alternatively use GitHub CLI, for example: `gh issue create --repo younesStrittmatter/psyneulink-corpus --label feedback --template tool_feedback` (adapt owner/repo if you use a fork).

More detail: [`docs/ISSUE_WORKFLOW.md`](ISSUE_WORKFLOW.md).

---

## Propose a brainlike entry

Share a concise YAML description of an architectural or dynamical motif you want modeling agents (via the MCP) to treat as “brainlike.”

1. **Fork** this repository (or use a branch if you maintain access).
2. Add a single file **`community/brainlike/<slug>.yaml`** where `<slug>` matches the file’s **`id`** field (kebab-case, unique in that folder).
3. Open a pull request against `main`. Use the PR checklist in the pull request template to self-review.
4. **CI** validates every YAML under `community/brainlike/` (except `_schema.yaml` itself) against [`community/brainlike/_schema.yaml`](../community/brainlike/_schema.yaml). Fix validation errors before requesting review.

Maintainers listed in `.github/CODEOWNERS` review proposals. Prefer concrete **`examples`** (PsyNeuLink-oriented snippets or configuration sketches) over vague prose; see [`docs/BRAINLIKE_SCHEMA.md`](BRAINLIKE_SCHEMA.md).
