# psyneulink-corpus

Community-curated **brainlike** YAMLs and the canonical issue queue for
human-reported tool feedback in the `psyneulink-ai` stack.

This repo is **data + review rules**, not code. Nothing here imports
PsyNeuLink. No Python tooling lives here.

## Working with Claude on this project

I'm using this project to learn how to use Claude (Code, API, SDK, MCP)
efficiently and in a modern way. While we work:

- **Surface better tools.** When I do something the long way, point out the
  faster idiom — slash commands, skills, subagents, hooks, MCP, plan/auto
  mode, parallel tool calls, the right model for the job (Haiku/Sonnet/Opus).
- **Suggest, don't silently do.** If you spot a more idiomatic approach
  mention it briefly so I can choose.
- **Flag anti-patterns.** Call out when I'm building something redundant or
  violating the separation of concerns.
- **Be concrete.** Prefer "use `/skill X` instead of …" over generic advice.

This applies to *how we collaborate*, not the runtime architecture (this
repo has no runtime — it's data).

## Architecture (three repos)

Three sibling repos under `~/Documents/code/AutoGrad/psyneulink-ai/`:

- **`psyneulink-mcp`:** a passive MCP server wrapping PsyNeuLink. No LLM at
  runtime. *Reads* this repo (brainlike YAMLs + open `feedback`-labeled
  issues) but never writes to it.
- **`psyneulink-corpus` (this repo):** community brainlike definitions +
  human-filed tool feedback Issues. The only home for this data.
- **`psyneulink-agent`:** the modeling agent (Layer 2). Accesses corpus
  data only through MCP tools, never directly.

## Separation of concerns is pure (hard rule)

- This repo must contain **only** YAML data + review/contribution rules.
  If a `.py` file appears here, it doesn't belong.
- The MCP reads from here; it never writes from server code. The MCP's
  *generator* (build-time, not runtime) does post comments + add labels via
  `gh` after a successful regen — that is the *only* sanctioned write path.
- The agent never touches this repo directly.

If a feature seems to need to live here *and* in `psyneulink-mcp`, that's
a signal the design is wrong — not that we should make an exception.

## Multi-repo dev sessions: switch workspace first

A *multi-repo dev session* authors changes in more than one of the
three sibling repos in one sitting (e.g., spawning subagents that
operate in `../psyneulink-mcp/` or `../psyneulink-agent/`, or
coordinating a label rename that has to land in two repos together).

If you find you need one, **stop and ask the user to open a new Cursor
chat at the parent folder**:

```
~/Documents/code/AutoGrad/psyneulink-ai/
```

That folder has its own `AGENTS.md` and is the correct workspace for
multi-repo dev sessions. The shell sandbox restricts writes to the
workspace root; running cross-repo writes from this sub-repo workspace
forces a permission prompt for every shell call into a sibling. Don't
work around it with `required_permissions: ["all"]` — switch workspaces
once, work freely thereafter.

This is *not* the same as the forbidden cross-repo coupling above. A
multi-repo dev session produces independent commits in independent
repos that each respect the boundary. **Smell test:** if the work
would survive being done in two separate chats on different days with
no shared state, it's a dev-session convenience. If it requires
runtime/import coupling between repos, the polyrepo rule applies and
the design is wrong — fix the design.

## Two contribution flows

1. **File a tool feedback issue** — report a problem with a tool exposed by
   `psyneulink-mcp`: misleading description, schema mismatch, missing arg,
   wrong behavior. Use the `tool_feedback` issue template; the `feedback`
   label is applied automatically. The MCP's generator pulls open issues
   with this label on each regen and includes them in the LLM prompt.
2. **Propose a brainlike entry** — open a PR adding a YAML under
   `community/brainlike/<slug>.yaml`. The PR template walks you through the
   schema. CI validates the YAML against `_schema.yaml` before merge.

A **GitHub account is required** for both. See `docs/CONTRIBUTING.md`.

## Coupling with the MCP (read this before changing the issue template)

The MCP parses issue bodies in `src/psyneulink_mcp/corpus.py` using the
exact field labels the issue form produces. The labels currently expected:

```
Tool name
Issue type
Description
Suggested fix
Agent context
```

If you rename a label here, update `_LABEL_TO_KEY` in the MCP repo at the
same time. Otherwise feedback issues silently lose their structured fields
and fall back to "tool_name: unknown".

## Layout

```
community/brainlike/     community-curated YAML definitions (one per concept)
  _schema.yaml           the JSON-schema-style validation schema
  examples/              starter examples for contributors
users/<gh-handle>/       optional per-user brainlike profiles (if shared)
docs/                    CONTRIBUTING.md, BRAINLIKE_SCHEMA.md, ISSUE_WORKFLOW.md
.github/                 ISSUE_TEMPLATE/, PULL_REQUEST_TEMPLATE.md,
                         CODEOWNERS, workflows/validate.yml
plans/                   in-flight build plans (delete when implemented)
```

## Conventions

- Every YAML in `community/brainlike/` validates against `_schema.yaml`. CI
  enforces this on PRs.
- Issue labels: `feedback` (human-filed tool issue), `consumed` (pulled
  into a regen by the MCP generator).
- IDs in `community/brainlike/<id>.yaml` are kebab-case and globally
  unique within the directory.
- No Python here.
