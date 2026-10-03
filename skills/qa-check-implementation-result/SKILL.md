---
name: qa-check-implementation-result
description: Use when completed implementation work must be compared with the plan, task status, and expected test evidence before sign-off.
---

# Check Implementation Result

Compare the delivered implementation against the approved plan and the available execution evidence to determine whether the work is review-ready.

## Incoming artifacts

- Final implementation plan
- Task list or stage checklist
- Code diff, branch, or pull request
- Any test or check output
- Any implementation-stage summary produced by the developer

## Supporting files

- Read [checklist.md](checklist.md) as the implementation-result checklist template.

## Produce

Return a filled implementation-result checklist and a gaps summary for the caller to use in the later workflow stage.

## Workflow

1. Read the implementation-result checklist.
2. Review the approved plan, completed-task evidence, and delivered code evidence together.
3. Check whether the delivered result matches the claimed completed work.
4. Mark each checklist item as fully addressed, partially addressed, or not addressed.
5. Summarize any missing deliverables, missing evidence, or unresolved risks.

## Output format

Use this structure:

```text
[x] ...
Answer: <evidence from plan, tasks, diff, or checks>

[~] ...
Answer: Partial — <what is present>. Missing: <what is still unproven or absent>.

[ ] ...
Answer: Not addressed.
```

Append:

```text
Gaps summary:
- <every partial or missing item>
```

## Human checkpoints

- Missing evidence or unresolved gaps will be reviewed before sign-off.
- If more implementation happens, this check will be re-run against the updated result.

## Rules

- Compare the result against the approved plan, not against imagined future work.
- Do not assume that a task is complete just because it is marked done.
- Be explicit when the implementation may be correct but the evidence is incomplete.
