# Copilot Instructions for shift-left-dev

This is a Claude Code plugin marketplace containing the **shift-left-dev** plugin — a 6-stage feature-development workflow implementing Shift-Left Testing principles.

## Key Points About This Repository

**No build, test, or linter.** This repo contains only markdown definitions — no application code, no CI/CD, no test runner. Changes are validated through careful reading and consistency checks, not automated tests.

**Markdown is the entire product.** Every deliverable is a `.md` file: commands, subagents, skills, and artifact templates.

## Four-Layer Architecture

The plugin is a strict four-layer system where each layer has a single responsibility and always invokes downward only (never sideways):

1. **Commands** (`commands/slt*.md`) — slash-command entrypoints. Each defines one workflow stage, prerequisite artifacts, steps (almost always delegating to a subagent), output artifacts, and a 🛑 human checkpoint at the end.
   - Main entry: `/slt` (routes to the correct stage based on task progress)
   - Stage commands: `/slt-01-kickoff`, `/slt-02-focus`, `/slt-03-plan`, `/slt-04-implement`, `/slt-05-review`, `/slt-06-polish`
   - Utility: `/slt-checklist` (runs QA checklist for the current stage)

2. **Subagents** (`agents/{pm,dev,qa}.md`) — three role-scoped agents invoked by commands via the `Task` tool. Each declares its allowed tools and model in frontmatter.
   - `pm` — Product Manager: owns requirements and acceptance criteria; restricted to reading user input and `my-specs/product-docs/`
   - `dev` — Senior Developer: owns codebase exploration, planning, implementation, code review
   - `qa` — QA Expert: owns checklists, test design, gap analysis (never implements)

3. **Skills** (`skills/<role>-<verb>/SKILL.md`) — reusable procedures invoked by name from subagent prompts. Each skill has:
   - Frontmatter: `name` + `description`
   - `Incoming artifacts` section (what this skill reads)
   - `Produce` template (what this skill outputs)
   - Numbered `Workflow` (step-by-step instructions)
   - `Rules` (do's and don'ts specific to this skill)
   - QA checklist skills also have `checklist.md` templates that the skill populates

4. **Artifacts** — markdown files in `my-specs/tasks/<short-name>/` with stage-numbered prefixes. These files are the state flow between stages and are accumulative: each stage reads ALL upstream artifacts plus its own.

**When editing, prefer the lowest layer that fits:** modify a skill's workflow rather than a command, add a new skill rather than embedding logic into a subagent prompt.

## Artifact Flow and Stage Order

Stages are strictly ordered `01 → 06`. The umbrella `/slt` command reads `my-specs/tasks/<short-name>/` to detect which artifacts exist and routes to the next stage automatically.

| Stage | Artifacts produced | Notes |
|-------|-------------------|-------|
| 01 Kickoff | `business-idea.md`, `implementation-scope.md`, `01-kickoff-checklist.md` | Explores business idea, defines scope |
| 02 Focus | `development-plan.md`, `testing-plan.md`, `automation-plan.md`, `02-focus-checklist.md` | Drafts plans for implementation, testing, automation |
| 03 Plan | `implementation-plan.md`, `tasks.md`, `03-plan-checklist.md` | Compiles final plans and executable task list |
| 04 Implement | `04-tasks-progress.md` | **Copies** `03-tasks.md` on first run, then mutates as work proceeds; `03-tasks.md` stays immutable |
| 05 Review | `dev-review.md`, `qa-review.md`, `review-summary.md` | Developer code review + QA implementation-result check |
| 06 Polish | `06-done-checklist.md` | Addresses follow-ups, confirms Definition of Done |

Each stage reads ALL upstream artifacts (accumulative context) and gates on its checklist before the next stage runs.

## Critical Conventions

**No invention.** Every artifact must be grounded in the supplied input or the codebase. Missing information is flagged as an explicit gap, not made up. This rule appears in `agents/pm.md`, every skill, and commands — keep it consistent if you add new skills.

**Human-in-the-loop checkpoints.** Every command ends with a 🛑 review section that hands control back to the user. Do not remove or auto-resolve checkpoints.

**Implement all stages, review once at the end.** Stage 04 executes remaining stages one at a time in a loop, committing each as a standalone commit, with **no human review between stages**. After the last stage, it deploys to the dev environment, then gates on a single 🛑 checkpoint. The loop stops early only on an unsafe blocker.

**Subagent boundaries.** Don't ask `pm` for code, don't ask `qa` to implement, don't bypass `dev` for code review. The role split is load-bearing for the workflow's quality model.

**Parallel vs sequential delegation.** Some commands require parallel `Task` calls (e.g., `/slt-01-kickoff` Steps 1a/1b); others require strict sequential delegation because each step consumes the previous step's output. Always read the command's "Steps" preamble before reordering.

**Frontmatter is contract.** Commands declare `description` + `argument-hint`; subagents declare `name`, `description`, `tools`, `model`; skills declare `name` + `description`. The `description` is what Claude Code uses to surface the item — keep it specific and trigger-oriented.

**Short-name derivation.** New tasks get a kebab-case `<short-name>` derived from the title, prefixed with the Jira key when present (e.g., `JIRA-123-add-two-factor-auth`). Always confirm with the user before creating the directory.

## Jira Integration

Several stages interact with Jira through the **Atlassian MCP server**:
- **Stage 01** — the `pm` subagent follows Jira issue parents and linked issues to discover context; the readiness checklist flags a missing parent as a blocker for non-bug issues
- **Stage 06** — outcome comments are posted to the main issue and open subtasks

These stages assume the Atlassian MCP server is connected and registered as **`claude_ai_Atlassian`**. The `pm` subagent's `tools` list references this name explicitly. If your server is registered under a different name, update the `mcp__<server>__*` entries in `agents/pm.md` to match. Without the server, Jira-dependent steps are skipped.

## File Structure

```
.
├── .claude-plugin/
│   ├── marketplace.json       # Registers this as a Claude Code marketplace
│   └── plugin.json            # Declares the shift-left-dev plugin
├── agents/                    # Three role-scoped subagents
│   ├── pm.md                  # Product Manager
│   ├── dev.md                 # Senior Full-Stack Developer
│   └── qa.md                  # QA Expert
├── commands/                  # 8 slash-command entrypoints
│   ├── slt.md                 # Umbrella router
│   ├── slt-01-kickoff.md
│   ├── slt-02-focus.md
│   ├── slt-03-plan.md
│   ├── slt-04-implement.md
│   ├── slt-05-review.md
│   ├── slt-06-polish.md
│   └── slt-checklist.md       # Runs the appropriate QA checklist
├── skills/                    # 13 reusable procedures
│   ├── pm-prepare-task-requirements/
│   ├── dev-explore-codebase/
│   ├── dev-prepare-development-plan/
│   ├── dev-prepare-automation-plan/
│   ├── dev-prepare-implementation-plan/
│   ├── dev-implement/
│   ├── dev-code-review/
│   ├── qa-check-definition-of-ready/
│   ├── qa-check-plans-consistency/
│   ├── qa-prepare-test-plan/
│   ├── qa-check-implementation-result/
│   ├── qa-check-definition-of-done/
│   └── qa-report-outcomes/
├── my-specs/                  # Task artifacts (git-ignored; generated at runtime)
│   └── tasks/
│       └── <short-name>/      # One directory per task
├── README.md
├── CLAUDE.md                  # Detailed developer guidance (read first when editing)
└── .gitignore
```

## Tips for Making Changes

1. **Read CLAUDE.md first** — it contains comprehensive guidance on conventions, artifact flow, and architecture details.

2. **When adding a new skill**, create a directory `skills/<role>-<verb>/` with:
   - `SKILL.md` (the procedure definition)
   - Optional: `checklist.md` (for QA checklist skills only)
   - Follow the template: frontmatter → Incoming artifacts → Produce → Workflow → Rules

3. **When editing a command**, check its "Steps" preamble: does it require parallel or sequential delegations? Reordering steps can break the artifact flow.

4. **To update Jira tool references** in `agents/pm.md`, find all `mcp__<server>__*` entries and update `<server>` to match your Atlassian MCP server registration name.

5. **Test logic paths by reading through the artifact flow**: trace what each stage reads and produces. Verify that downstream stages have access to what they need.

6. **Before committing**, ensure the change respects the role boundaries (pm/dev/qa) and does not invent information where a gap should be flagged.
