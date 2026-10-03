# AGENTS.md

Guidance for agents working in this repository.

## What this repo is

An **OpenCode customization bundle** that implements a 6-stage feature-development
workflow ("Shift-Left Testing"). The repo contains no application code — only
markdown definitions for commands, subagents, and skills. There is no build, test
runner, or linter; changes are validated by careful reading and consistency checks.

- `.opencode/agents/` — three role-scoped subagents
- `.opencode/commands/` — eight slash-command entrypoints
- `.opencode/skills/` — thirteen reusable procedures (five QA skills ship a `checklist.md` template)
- `opencode.jsonc` — optional project configuration (includes a commented Atlassian MCP example)

## Local development

OpenCode discovers `.opencode/` automatically when it is run from this directory:

```bash
opencode
```

To use the bundle in another project, copy or symlink `.opencode/` into that
project's root. For a machine-wide install, copy the subdirectories into the
global OpenCode config:

```bash
mkdir -p ~/.config/opencode
cp -R .opencode/agents .opencode/commands .opencode/skills ~/.config/opencode/
```

There is no marketplace or plugin-install step.

## Architecture: Commands → Subagents → Skills → Artifacts

The bundle is a four-layer system. Each layer has a single responsibility and the
layers always invoke downward — never sideways:

1. **Commands** (`.opencode/commands/slt*.md`) — slash-command entrypoints. Each
   defines one workflow stage, the prerequisite artifacts it must read, the steps
   it runs (almost always by delegating to a subagent), the output artifacts it
   saves, and a 🛑 human checkpoint at the end. Commands are prompt templates; the
   `$ARGUMENTS` placeholder is replaced with the text the user passes.
2. **Subagents** (`.opencode/agents/{pm,dev,qa}.md`) — three role-scoped agents.
   Commands invoke them with the `subagent` tool, passing the agent ID. Each agent
   declares `mode: subagent` and an ordered `permissions` list in frontmatter.
   - `pm` — Product Manager. Owns the "WHAT" (requirements, acceptance criteria).
     Restricted to reading/writing artifacts plus the Atlassian MCP.
   - `dev` — Senior Full-Stack Developer. Owns the "HOW" (codebase exploration,
     plans, implementation, code review).
   - `qa` — QA Expert. Owns checklists, test design, gap analysis. Does **not**
     implement.
3. **Skills** (`.opencode/skills/<role>-<verb>/SKILL.md`) — reusable procedures
   loaded by exact ID with the `skill` tool (e.g. `dev-implement`). Each has a
   frontmatter `name` + `description`, an `Incoming artifacts` section, a `Produce`
   template, a numbered `Workflow`, and `Rules`. QA checklist skills additionally
   ship a `checklist.md` template that the skill fills in.
4. **Artifacts** — every stage writes markdown files to
   `my-specs/tasks/<short-name>/` using stage-numbered prefixes. These files are how
   state flows between stages.

When adding or modifying behavior, prefer the lowest layer that fits: tweak a
skill's workflow rather than a command, and add a new skill rather than embedding
logic into a subagent prompt.

## Workflow stages and the artifact contract

Stages are strictly ordered `01 → 06`. Each stage's command reads all upstream
artifacts (accumulative context), produces its own, and gates on a checklist before
the next stage runs. The umbrella `/slt` command routes by inspecting which
prefixes exist in `my-specs/tasks/<short-name>/`.

| Stage | Command | Files produced |
|-------|---------|----------------|
| 01 Kickoff | `/slt-01-kickoff` | `01-business-idea.md`, `01-implementation-scope.md`, `01-kickoff-checklist.md` |
| 02 Focus | `/slt-02-focus` | `02-development-plan.md`, `02-testing-plan.md`, `02-automation-plan.md`, `02-focus-checklist.md` |
| 03 Plan | `/slt-03-plan` | `03-implementation-plan.md`, `03-tasks.md`, `03-plan-checklist.md` |
| 04 Implement | `/slt-04-implement` | `04-tasks-progress.md` (working copy of `03-tasks.md`) |
| 05 Review | `/slt-05-review` | `05-dev-review.md`, `05-qa-review.md`, `05-review-summary.md` |
| 06 Polish | `/slt-06-polish` | `06-done-checklist.md`, `06-outcome-comments.md` |

Cross-cutting rule: `/slt-checklist` is a dispatcher that re-runs the appropriate
QA checklist skill for whichever stage is current. It overwrites the existing
checklist file rather than versioning.

Stage-04 has a unique pattern: `04-tasks-progress.md` is created by **copying**
`03-tasks.md` on first run, then mutated as work proceeds. The original
`03-tasks.md` stays immutable.

## Conventions to preserve when editing

- **No invention.** Every artifact must be grounded in the supplied input or the
  codebase. Missing information is flagged as an explicit gap, not filled in. This
  rule appears in `pm`, every skill, and every command — keep it consistent if you
  add new skills.
- **Implement all stages, review once at the end.** `/slt-04-implement` executes the
  remaining stages **one at a time in a loop** (delegating each to `dev` via
  `dev-implement`), committing each stage as a standalone commit, with **no human
  review between stages**. After the last stage it deploys to the dev environment,
  then gates on a single 🛑 checkpoint. The loop stops early only on an unsafe
  blocker.
- **Human-in-the-loop checkpoints.** Every command ends with a 🛑 review section
  that hands control back to the user. Do not remove or auto-resolve checkpoints.
- **Subagent boundaries.** Don't ask `pm` for code, don't ask `qa` to implement,
  don't bypass `dev` for code review. The role split is load-bearing for the
  workflow's quality model.
- **Parallel vs sequential delegation.** Some commands (e.g. `/slt-01-kickoff`
  Steps 1a/1b) explicitly require parallel `subagent` calls in a single assistant
  turn. Other commands (e.g. `/slt-02-focus`) require strict sequential delegation
  because each step consumes the previous step's artifact. Read the command's
  "Steps" preamble before reordering.
- **Frontmatter is contract.** Commands declare `description` (and may use `agent`,
  `model`, `subagent`); subagents declare `description`, `mode: subagent`, and
  `permissions`; skills declare `name` + `description`. The `description` is what
  OpenCode uses to surface the item — keep it specific and trigger-oriented.
- **Permission rules are last-match-wins.** In an agent's `permissions` list,
  place the broad default rule `{ action: "*", resource: "*", effect: "deny" }`
  **first**, then the specific `allow` rules (e.g. `read`, `edit`, `skill`). A
  trailing wildcard `deny` overrides every earlier `allow`, silently leaving the
  agent with **no tools** — the subagent then completes without doing any work.
- **Short-name derivation.** New tasks get a kebab-case `<short-name>` derived from
  the title, prefixed with the Jira key when present (e.g.
  `JIRA-123-add-two-factor-auth`). Confirm with the user before creating the
  directory.

## Jira integration (Atlassian MCP)

Several stages interact with Jira through the **Atlassian MCP server**:

- **Stage 01 (Kickoff)** — the `pm` subagent follows a Jira issue's parent and linked
  issues (and referenced Confluence pages) to discover the broader context, and the
  readiness checklist flags a missing parent as a blocker for any non-bug issue type.
- **Stage 06 (Polish)** — outcome comments are posted to the main issue and open
  subtasks.

The bundle assumes the server is registered under the name **`atlassian`** under
`mcp.servers` in `opencode.jsonc`. OpenCode names MCP tools `<server>_<tool>`, so the
Atlassian tools appear as `atlassian_<tool>` (e.g. `atlassian_getJiraIssue`); the
`pm` agent's `permissions` allow the `atlassian_*` action. If your server is
registered under a different name, update the `atlassian_*` permission in
`.opencode/agents/pm.md` and the server entry in `opencode.jsonc`. Without the
server, the Jira-dependent steps are skipped (or run against whatever issue content
is pasted in manually).

## Tips for making changes

1. **Read this file first** — it contains the conventions, artifact flow, and
   architecture details.
2. **When adding a new skill**, create a directory `.opencode/skills/<role>-<verb>/`
   with `SKILL.md`, plus an optional `checklist.md` for QA checklist skills. Follow
   the template: frontmatter → Incoming artifacts → Produce → Workflow → Rules.
3. **When editing a command**, check its "Steps" preamble: does it require parallel
   or sequential delegation? Reordering steps can break the artifact flow.
4. **To update Jira tool references**, adjust the `atlassian_*` rule in
   `.opencode/agents/pm.md` and the `mcp.servers` entry in `opencode.jsonc`.
5. **Test logic paths by reading through the artifact flow**: trace what each stage
   reads and produces. Verify that downstream stages have access to what they need.
6. **Before committing**, ensure the change respects the role boundaries
   (pm/dev/qa) and does not invent information where a gap should be flagged.
