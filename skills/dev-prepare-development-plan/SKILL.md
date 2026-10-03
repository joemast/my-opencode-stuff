---
name: dev-prepare-development-plan
description: Use when the workflow stage needs an implementation plan based on the business idea, implementation scope, and kickoff findings.
---

# Prepare Development Plan

Create the developer-facing plan that explains what needs to be built and how the work should be broken down before the final implementation plan is compiled.

## Incoming artifacts

- Business idea / task requirements
- Implementation scope
- Kickoff checklist or equivalent readiness findings

## Produce

Return a development plan for the caller to store as a workflow stage artifact.

Use this structure:

```markdown
# Development Plan: <title>

## Goal
## Proposed Stages
### Stage 1
- Objective
- Main code changes
- Dependencies
### Stage N
## Detailed Work Breakdown
## Code Sketches
## Testability Notes
## Risks
## Assumptions and Open Questions
```

In **Code Sketches**, provide illustrative, low-fidelity snippets that let a human design-review the approach early: proposed function/method signatures, interfaces or type definitions, data structures, class skeletons, API/route contracts, key data shapes, or pseudo-code for non-obvious logic. These are design sketches to communicate intent and shape — not working implementations.

## Workflow

1. Read all incoming artifacts together before proposing work.
2. Translate the business idea and implementation scope into concrete technical work.
3. Break the work into ordered stages that a developer can execute incrementally.
4. Provide illustrative code sketches for the key changes (signatures, interfaces, data structures, contracts, or pseudo-code) so the human can design-review the approach at this early stage, before any implementation.
5. Include explicit testing hooks and unit-testability expectations in the plan.
6. Surface assumptions, blockers, or missing information instead of filling gaps with guessed requirements.

## Human checkpoints

- The plan will be reviewed before QA prepares the detailed test plan.
- Unresolved scope questions will be answered before the final implementation plan is compiled.

## Rules

- Keep the plan aligned with the supplied business idea and implementation scope.
- Do not invent extra features, rollout steps, or cleanup work that the inputs do not support.
- Prefer staged, reviewable work over one large undifferentiated task list.
- Treat code testability as mandatory, especially at the unit-test level where reasonable.
- Code sketches must stay illustrative (signatures, skeletons, pseudo-code) — they communicate design intent for review, not finished, runnable implementations.
- Do not implement anything, just plan.
