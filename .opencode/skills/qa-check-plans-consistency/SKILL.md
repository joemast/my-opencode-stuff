---
name: qa-check-plans-consistency
description: Use when the workflow stage has draft planning artifacts that must be checked for gaps, contradictions, or missing alignment before final planning.
---

# Check Plans Consistency

Compare the supplied planning artifacts to confirm that they describe the same scope, priorities, and readiness level before the final plan is compiled.

## Incoming artifacts

- Business idea / task requirements
- Implementation scope
- Implementation plan draft
- Testing plan draft
- Test automation draft

## Supporting files

- Read [checklist.md](checklist.md) as the consistency checklist template.

## Produce

Return a filled consistency checklist and a gaps summary for the caller to store as the workflow stage QA artifact.

## Workflow

1. Read the consistency checklist.
2. Review all supplied planning artifacts together.
3. Check whether they describe the same scope, priorities, terminology, and open questions.
4. Mark each checklist item as fully addressed, partially addressed, or not addressed.
5. Summarize contradictions, blind spots, and follow-up questions for human review.

## Output format

Use this structure:

```text
[x] ...
Answer: <evidence from artifacts>

[~] ...
Answer: Partial — <what aligns>. Missing or conflicting: <what does not>.

[ ] ...
Answer: Not addressed.
```

Append:

```text
Gaps summary:
- <every partial or missing item>
```

## Human checkpoints

- The caller will review the gaps summary before compiling the final implementation plan.
- If the artifacts change materially, this check will be re-run.

## Rules

- Use only the supplied planning artifacts.
- Do not reconcile contradictions by guessing which draft is "probably right."
- Prefer specific, cross-artifact comparisons over vague feedback.
