---
name: dev-prepare-implementation-plan
description: Use when the workflow stage needs to compile the final implementation plan and executable task list from the approved draft artifacts.
---

# Prepare Final Implementation Plan

Compile the approved planning artifacts into the final implementation plan and the executable task list used by later workflow stages.

## Incoming artifacts

- Business idea / task requirements
- Implementation scope
- Kickoff checklist
- Implementation plan draft
- Testing plan draft
- Test automation draft
- Plans consistency check, if available

## Produce

Return two coordinated outputs for the caller to store:

1. `implementation-plan`
2. `tasks`

Use this structure:

```markdown
# Implementation Plan: <title>

## Goal
## Version Control Strategy
## Delivery Stages
### Stage 1: <name>
- Scope
- Implementation tasks
- Test tasks
- Automation tasks
- Non-coding tasks
- Dependencies
### Stage N
## Cross-Stage Risks and Notes
```

In **Version Control Strategy**, define:
- The **working branch** to create before implementation should have name that meets the project requirements, branched off **`main`/`master`** by default.
- That **each implementation stage gets its own standalone commit** with a clear, conventional message, so stages are individually reviewable and revertible.
- Whether any stages warrant **their own separate branch** instead of just a commit — recommend this when a stage is large, risky, or must be mergeable/rollback-able independently of the others. State the rationale; if per-stage branches are not warranted, say a single working branch with per-stage commits is sufficient. When a separate branch **needs the changes** of a preceding stage/branch, derive it from that previous branch; otherwise derive it from `main`.

```markdown
# Tasks: <title>

## Stage 0: Setup
- [ ] Create working branch `<proposed-name>` off `main`

## Stage 1: <layer name>
- [ ] Task 1
- [ ] Task 2
- [ ] Commit Stage 1 as a standalone commit: "<conventional message>"

## Stage 2: <layer name>
- [ ] Task 3
- [ ] Commit Stage 2 as a standalone commit: "<conventional message>"
```

If the plan recommends a separate branch for a stage, add explicit "Create branch …" and "Merge branch … back into the working branch" tasks bracketing that stage instead of a plain commit task.

## Workflow

1. Read all approved planning artifacts together.
2. Merge them into one final plan without losing important detail from earlier drafts.
3. Group the work into implementation stages identified by product architecture layers such as data/storage, backend/services, shared utilities, business logic, frontend, or other applicable layers.
4. Produce a matching task list grouped by those same stages so later implementation can resume stage by stage.
5. Preserve implementation, testing, automation, and non-coding work as explicit tasks.
6. Define the version control strategy: a working branch created before implementation, a standalone commit per stage, and—where a stage is large, risky, or must be independently mergeable/revertible—a proposal to give that stage its own branch. Materialize these as explicit tasks (branch creation, per-stage commits, and any per-stage branch/merge tasks) so Stage 4 executes them.

## Human checkpoints

- The final implementation plan will be confirmed by human before implementation starts.
- If source artifacts disagree, return the conflict to the human instead of silently picking one version.

## Rules

- Do not drop requirements, testing work, or non-coding tasks from the incoming artifacts.
- Do not add unsupported tasks that are not justified by the source artifacts.
- Keep tasks specific enough to execute and verify.
- Keep stage boundaries explicit so later workflows can stop and resume safely.
- Make the stage breakdown layer-oriented; if a common layer does not apply, say so explicitly instead of silently omitting the rationale.
- Always include a working-branch creation step before the first implementation stage and a standalone commit task for every stage; never plan implementation directly on the base branch.
- Derive the first working branch from `main` by default. Derive any additional separate branch from the previous branch when it must contain that branch's changes; otherwise branch it from `main`.
- Treat per-stage branches as a recommendation, not a default — justify them by stage size, risk, or independent rollback/merge needs, and otherwise prefer one working branch with per-stage commits.
- Do not implement anything, just plan.
