# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code marketplace whose single plugin, `shift-left-dev`, implements a 6-stage feature-development workflow ("Shift-Left Testing"). The repo contains no application code — only markdown definitions for commands, subagents, and skills. There is no build, test runner, or linter.

- `.claude-plugin/marketplace.json` — registers this directory as a marketplace
- `.claude-plugin/plugin.json` — declares the `shift-left-dev` plugin (consumes `agents/`, `commands/`, `skills/` by Claude Code convention)

## Local development

```bash
claude --plugin-dir /path/to/my-claude-stuff
```

Or, from inside Claude Code, install via the marketplace:

```text
/plugin marketplace add joemast/my-claude-stuff
/plugin install shift-left-dev@my-claude-stuff
```

## Architecture: Commands → Subagents → Skills → Artifacts

The plugin is a four-layer system. Each layer has a single responsibility and the layers always invoke downward — never sideways:

1. **Commands** (`commands/slt*.md`) — slash-command entrypoints. Each defines one workflow stage, the prerequisite artifacts it must read, the steps it runs (almost always by delegating to a subagent), the output artifacts it saves, and a 🛑 human checkpoint at the end.
2. **Subagents** (`agents/{pm,dev,qa}.md`) — three role-scoped agents. Commands invoke them with the `Task` tool. Each declares its allowed tools and model in frontmatter.
   - `pm` — Product Manager. Owns the "WHAT" (requirements, acceptance criteria). Restricted to reading user input + `my-specs/product-docs/`.
   - `dev` — Senior Full-Stack Developer. Owns the "HOW" (codebase exploration, plans, implementation, code review).
   - `qa` — QA Expert. Owns checklists, test design, gap analysis. Does **not** implement.
3. **Skills** (`skills/<role>-<verb>/SKILL.md`) — reusable procedures invoked by name from a subagent's prompt (e.g. "Using the `dev-implement` skill, …"). Each has a frontmatter `name` + `description`, an `Incoming artifacts` section, a `Produce` template, a numbered `Workflow`, and `Rules`. QA checklist skills additionally ship a `checklist.md` template that the skill fills in.
4. **Artifacts** — every stage writes markdown files to `my-specs/tasks/<short-name>/` using stage-numbered prefixes. These files are how state flows between stages.

When adding or modifying behavior, prefer the lowest layer that fits: tweak a skill's workflow rather than a command, and add a new skill rather than embedding logic into a subagent prompt.

## Workflow stages and the artifact contract

Stages are strictly ordered `01 → 06`. Each stage's command reads all upstream artifacts (accumulative context), produces its own, and gates on a checklist before the next stage runs. The umbrella `/slt` command routes by inspecting which prefixes exist in `my-specs/tasks/<short-name>/`.

| Prefix | Stage | Command | Files produced |
|--------|-------|---------|----------------|
| `01-` | Kickoff | `/slt-01-kickoff` | `business-idea.md`, `implementation-scope.md`, `kickoff-checklist.md` |
| `02-` | Focus | `/slt-02-focus` | `development-plan.md`, `testing-plan.md`, `automation-plan.md`, `focus-checklist.md` |
| `03-` | Plan | `/slt-03-plan` | `implementation-plan.md`, `tasks.md`, `plan-checklist.md` |
| `04-` | Implement | `/slt-04-implement` | `tasks-progress.md` (working copy of `03-tasks.md`) |
| `05-` | Review | `/slt-05-review` | `dev-review.md`, `qa-review.md`, `review-summary.md` |
| `06-` | Polish | `/slt-06-polish` | `done-checklist.md` |

Cross-cutting rule: `/slt-checklist` is a dispatcher that re-runs the appropriate QA checklist skill for whichever stage is current. It overwrites the existing checklist file rather than versioning.

Stage-04 has a unique pattern: `04-tasks-progress.md` is created by **copying** `03-tasks.md` on first run, then mutated as work proceeds. The original `03-tasks.md` stays immutable.

## Conventions to preserve when editing

- **No invention.** Every artifact must be grounded in the supplied input or the codebase. Missing information is flagged as an explicit gap, not filled in. This rule appears in `agents/pm.md`, every skill, and every command — keep it consistent if you add new skills.
- **Implement all stages, review once at the end.** `/slt-04-implement` executes the remaining stages **one at a time in a loop** (delegating each to `dev` via `dev-implement`), committing each stage as a standalone commit, with **no human review between stages**. After the last stage it deploys to the dev environment, then gates on a single 🛑 checkpoint. The loop stops early only on an unsafe blocker.
- **Human-in-the-loop checkpoints.** Every command ends with a 🛑 review section that hands control back to the user. Do not remove or auto-resolve checkpoints.
- **Subagent boundaries.** Don't ask `pm` for code, don't ask `qa` to implement, don't bypass `dev` for code review. The role split is load-bearing for the workflow's quality model.
- **Parallel vs sequential delegation.** Some commands (e.g. `/slt-01-kickoff` Steps 1a/1b) explicitly require parallel `Task` calls in a single message. Other commands (e.g. `/slt-02-focus`) require strict sequential delegation because each step consumes the previous step's artifact. Read the command's "Steps" preamble before reordering.
- **Frontmatter is contract.** Commands declare `description` + `argument-hint`; subagents declare `name`, `description`, `tools`, `model`; skills declare `name` + `description`. The `description` is what Claude Code uses to surface the item — keep it specific and trigger-oriented.
- **Short-name derivation.** New tasks get a kebab-case `<short-name>` derived from the title, prefixed with the Jira key when present (e.g. `JIRA-123-add-two-factor-auth`). Confirm with the user before creating the directory.
