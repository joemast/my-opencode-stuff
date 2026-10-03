# shift-left-dev (Claude Code plugin)

**Version**: 0.1.1

A Claude Code plugin implementing the **Shift-Left Testing** approach for feature development. 

The plugin orchestrates feature development through 6 sequential workflow stages with human-in-the-loop checkpoints, layered as Commands → Subagents (PM / Dev / QA) → Skills → artifact files.

## Workflow

| Stage | Command | Purpose |
|-------|---------|---------|
| — | `/slt` | Umbrella entrypoint — detects current progress and routes to the correct stage |
| 01 | `/slt-01-kickoff` | Explore the business idea, define scope, run the readiness checklist |
| 02 | `/slt-02-focus` | Draft implementation, testing, and automation plans |
| 03 | `/slt-03-plan` | Compile the final implementation plan and executable task list |
| 04 | `/slt-04-implement` | Execute one implementation stage at a time |
| 05 | `/slt-05-review` | Developer code review + QA implementation-result check |
| 06 | `/slt-06-polish` | Address follow-ups, confirm Definition of Done |
| — | `/slt-checklist` | Run the appropriate QA checklist for the current stage |

All artifacts are stored in `my-specs/tasks/<short-name>/` with stage-numbered filenames (`01-*.md`, `02-*.md`, …).

## Install

This repo is itself a Claude Code marketplace. From inside Claude Code:

```text
/plugin marketplace add <joemast/my-claude-staff.git>
/plugin install shift-left-dev@my-claude-stuff
```

For local development:

```bash
claude --plugin-dir /path/to/my-claude-stuff
```

## Jira integration (Atlassian MCP)

Several stages talk to Jira through the **Atlassian MCP server**:

- **Stage 01 (Kickoff)** — the `pm` subagent follows a Jira issue's parent and linked issues (and referenced Confluence pages) to discover the broader context, and the readiness checklist flags a missing parent as a blocker for any non-bug issue type.
- **Stage 06 (Polish)** — outcome comments are posted to the main issue and open subtasks.

These stages assume the Atlassian MCP server is connected and named **`claude_ai_Atlassian`** — the name produced when connecting Atlassian via claude.ai. The `pm` subagent's `tools` list references that name explicitly (e.g. `mcp__claude_ai_Atlassian__getJiraIssue`). If your Atlassian MCP server is registered under a different name, update the `mcp__<server>__*` entries in `agents/pm.md` to match. Without the server, the Jira-dependent steps are skipped (or run against whatever issue content you paste in manually).

## Components

- **3 subagents**: `pm` (product manager), `dev` (full-stack developer), `qa` (testing expert)
- **8 commands**: the 6 workflow stages plus `/slt` and `/slt-checklist`
- **13 skills**: PM (1) + Dev (6) + QA (6, five with `checklist.md` templates)

## Conventions

- **No invention**: every artifact uses only facts from supplied materials; gaps are flagged explicitly.
- **Human-in-the-loop**: every stage ends with a 🛑 review checkpoint.
- **Artifact-driven routing**: `/slt` reads the task directory to detect the next action.
- **Accumulative context**: each stage reads ALL upstream artifacts.

