---
name: qa-prepare-test-plan
description: Use when the workflow stage needs a prioritized test plan draft for a proposed feature before the final implementation plan is compiled.
---

# Prepare Test Plan Draft

Create the QA-facing testing draft that explains what should be verified, why it matters, and how the checks should be prioritized.

## Incoming artifacts

- Business idea / task requirements
- Implementation scope
- Implementation plan draft

## Produce

Return a testing-plan draft for the caller to store as a workflow stage artifact.

Use this structure:

```markdown
# Testing Plan Draft: <title>

## Test Objectives
## Key Risks
## Prioritized Checks
### High Priority
### Medium Priority
### Low Priority
## Test Design Notes
## Environment and Data Notes
## Open Questions
```

## Workflow

1. Read the business idea, implementation scope, and implementation plan draft together.
2. Identify the highest-risk product and technical scenarios to verify.
3. Build a prioritized test plan that covers functional, edge-case, and non-functional checks where relevant, structuring the plan according to risk and impact, grouping related checks together.
4. Use test design techniques where helpful, such as boundary analysis, equivalence partitioning, decision tables, pairwise reasoning, or state transitions.
5. Surface gaps, assumptions, and missing test inputs explicitly.

## Human checkpoints

- The draft test plan will be reviewed before automation planning and final plan compilation.
- Any high-risk gap will be discussed before implementation begins.

## Rules

- Keep priorities explicit.
- Do not invent unsupported product behavior.
- Tie each important check back to the supplied artifacts.
- Prefer practical, reviewable test coverage over vague "test everything" language.
