---
name: qa-check-implementation-plan
description: Use when the final implementation plan and task list must be checked for readiness, coverage, and alignment with requirements.
---

# Check Implementation Plan

Review the final implementation plan and task list to confirm that the team can execute them without losing requirements, tests, or important follow-up work.

## Incoming artifacts

- Final implementation plan
- Final task list
- Upstream planning artifacts for coverage comparison
  - Business idea / task requirements
  - Implementation plan draft
  - Testing plan draft
  - Test automation draft


## Supporting files

- Read [checklist.md](checklist.md) as the implementation-plan checklist template.

## Produce

Return a filled implementation-plan checklist plus a gaps summary for the caller to use before implementation starts.

## Workflow

1. Read the implementation-plan checklist.
2. Review the final implementation plan and task list.
3. Compare them with upstream requirements and planning artifacts.
4. Mark each checklist item as fully addressed, partially addressed, or not addressed.
5. Summarize missing coverage, unsupported tasks, or readiness problems.

## Output format

Use this structure:

```text
[x] ...
Answer: <evidence from plan or tasks>

[~] ...
Answer: Partial — <what is present>. Missing: <what is absent>.

[ ] ...
Answer: Not addressed.
```

Append:

```text
Gaps summary:
- <every partial or missing item>
```

## Human checkpoints

- The plan will not move to implementation until blockers from this check are resolved or explicitly accepted by humans.
- If the plan changes materially, this check will be re-run.

## Rules

- Do not invent missing plan content.
- Call out extra tasks that are not supported by the approved artifacts.
- Treat test, automation, and non-coding work as first-class plan content.
- Treat architecture-layer staging as a required property of the final implementation plan.
- Treat per-stage completion criteria or stage-level readiness checks as a required property of the final implementation plan.
- Treat the version control strategy as a required property of the plan: flag as a blocker any missing working-branch definition, missing working-branch creation task before the first stage, or any delivery stage lacking a standalone commit task. Where a stage is given its own separate branch, confirm the branch-creation and merge-back tasks and its base branch are present.
