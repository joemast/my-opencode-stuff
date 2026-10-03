---
name: dev-prepare-automation-plan
description: Use when the workflow stage needs a test automation draft that maps prioritized checks to automation levels and the test pyramid.
---

# Prepare Test Automation Draft

Turn the planned implementation and testing work into a pragmatic automation strategy that prioritizes the most valuable checks first.

## Incoming artifacts

- Business idea / task requirements
- Implementation plan
- Testing plan

## Produce

Return a test-automation plan for the caller to store as a workflow stage artifact.

Use this structure:

```markdown
# Test Automation Plan: <title>

## Automation Goals
## MUST Automate
## SHOULD Automate
## COULD Automate
## Recommended Test Levels
## Test Code Sketches
## Manual-Only or Deferred Checks
## Open Questions
```

In **Test Code Sketches**, provide illustrative, low-fidelity snippets for the most valuable (MUST-automate) checks so a human can design-review the testing approach early: test names/descriptions, arrange-act-assert outlines, fixtures or mocks needed, and the framework/level each would run at. These are design sketches to convey the testing shape — not working test implementations.

## Workflow

1. Review the business idea, implementation plan, and testing plan together.
2. Identify which checks are high, medium, and low priority from a risk perspective.
3. Map those checks to MUST, SHOULD, and COULD automation buckets.
4. Prefer low-level automated tests first, following the test automation pyramid.
5. Provide illustrative test code sketches for the MUST-automate checks (test names, arrange-act-assert outlines, fixtures/mocks, target level) so the human can design-review the testing approach early, before any test implementation.
6. Flag checks that are better left manual or deferred, and explain why.

## Human checkpoints

- The automation plan should be reviewed together with the implementation and testing plans before final plan compilation.
- Any expensive or disputed automation scope should be confirmed by the human before implementation starts.

## Rules

- High-priority checks must be proposed for automation.
- Medium-priority checks should be automated when the coverage is simple and maintainable.
- Low-priority checks should be automated only when they are simple, fast, and clearly worthwhile.
- Do not promise automation that the inputs or system constraints do not realistically support.
- Test code sketches must stay illustrative (names, AAA outlines, pseudo-code) — they communicate testing design for review, not finished, runnable tests.
- Do not implement anything, just plan.
