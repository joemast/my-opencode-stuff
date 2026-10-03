# shift-left-dev (OpenCode bundle)

**Version**: 0.1.1

An [OpenCode](https://opencode.ai) customization bundle implementing the
**Shift-Left Testing** approach for feature development.

The bundle orchestrates feature development through 6 sequential workflow stages
with human-in-the-loop checkpoints, layered as Commands → Subagents (PM / Dev / QA)
→ Skills → artifact files.

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

All artifacts are stored in `my-specs/tasks/<short-name>/` with stage-numbered
filenames (`01-*.md`, `02-*.md`, …).

## Install

This repo is an OpenCode bundle: agents, commands, and skills live under
`.opencode/`, which OpenCode discovers automatically.

**Use it in one project** — copy (or symlink) the definitions into the project
root:

```bash
cp -R /path/to/my-opencode-stuff/.opencode /path/to/your-project/.opencode
```

**Use it everywhere** — copy the subdirectories into the global OpenCode config:

```bash
mkdir -p ~/.config/opencode
cp -R /path/to/my-opencode-stuff/.opencode/agents \
      /path/to/my-opencode-stuff/.opencode/commands \
      /path/to/my-opencode-stuff/.opencode/skills \
      ~/.config/opencode/
```

Then run `opencode` and use `/slt-01-kickoff` (or the umbrella `/slt`) as a slash
command. There is no marketplace or plugin-install step.

## Jira integration (Atlassian MCP)

Several stages talk to Jira through the **Atlassian MCP server**:

- **Stage 01 (Kickoff)** — the `pm` subagent follows a Jira issue's parent and linked
  issues (and referenced Confluence pages) to discover the broader context, and the
  readiness checklist flags a missing parent as a blocker for any non-bug issue type.
- **Stage 06 (Polish)** — outcome comments are posted to the main issue and open
  subtasks.

The bundle assumes the server is registered under the name **`atlassian`** under
`mcp.servers`. OpenCode names MCP tools `<server>_<tool>`, so the Atlassian tools
appear as `atlassian_<tool>` (e.g. `atlassian_getJiraIssue`). The `pm` agent's
`permissions` allow the `atlassian_*` action.

Register the server with the CLI or by editing `opencode.jsonc` (a commented example
is provided):

```bash
opencode mcp add atlassian --url https://<your-atlassian-mcp-endpoint>/mcp
opencode mcp list
```

If your server is registered under a different name, update the `atlassian_*`
permission in `.opencode/agents/pm.md` to match. Without the server, the
Jira-dependent steps are skipped (or run against whatever issue content you paste in
manually).

## Components

- **3 subagents**: `pm` (product manager), `dev` (full-stack developer), `qa` (testing expert)
- **8 commands**: the 6 workflow stages plus `/slt` and `/slt-checklist`
- **13 skills**: PM (1) + Dev (6) + QA (6, five with `checklist.md` templates)

## Layout

```text
.opencode/
├── agents/          # pm, dev, qa (mode: subagent)
├── commands/        # slt, slt-01..06, slt-checklist
└── skills/          # 13 skills, each <id>/SKILL.md
AGENTS.md            # developer guidance for this repo
opencode.jsonc       # optional project config (Atlassian MCP example)
```

## Conventions

- **No invention**: every artifact uses only facts from supplied materials; gaps are flagged explicitly.
- **Human-in-the-loop**: every stage ends with a 🛑 review checkpoint.
- **Artifact-driven routing**: `/slt` reads the task directory to detect the next action.
- **Accumulative context**: each stage reads ALL upstream artifacts.
