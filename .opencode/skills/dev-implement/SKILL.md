---
name: dev-implement
description: Use when an approved implementation plan exists and the next unfinished implementation stage should be executed without replanning the whole feature.
---

# Implement the Next Stage

Execute only the next unfinished stage from the approved implementation plan, then stop and report the outcome for review.

## Incoming artifacts

- Final implementation plan
- Current progress state showing which tasks are already done
- Next stage to implement
- Relevant codebase context for the active stage usually available via Git

## Produce

Return a stage result for the caller to use in the implementation workflow.

Use this structure:

```markdown
# Stage Implementation Result: <stage name>

## Completed Work
## Tasks Ready To Mark Done
## Checks Run
## Follow-Up Notes
## Blockers or Open Questions
```

## Workflow

1. Read the implementation plan to get the whole picture.
2. Identify the task list for the next unfinished stage only.
3. Implement that stage without jumping ahead to later stages. Usually, you already work in the proper context (code branch) and have the necessary codebase information.
4. Run the relevant tests and checks for the completed stage, if defined.
5. Complete the stage's version-control task: commit the stage as a standalone commit (or create/merge a branch where the plan specifies one), using the message from the plan.
6. Summarize what changed, which tasks are ready to be marked done, and what remains uncertain.
7. Stop after the stage is complete and report, so the workflow can update progress and proceed to the next stage.

## Human checkpoints

- If the plan is missing detail needed for safe implementation, raise that gap instead of guessing.

## Rules

- Do not replan the whole feature.
- Do not implement multiple future stages in one pass.
- Do not claim tasks are done unless the change and checks support that claim.
- Report artifact updates that the workflow should make; do not invent or rewrite planning artifacts on your own.
- If the active stage includes non-coding tasks, note them clearly in `Follow-Up Notes` for the workflow or human to handle.
