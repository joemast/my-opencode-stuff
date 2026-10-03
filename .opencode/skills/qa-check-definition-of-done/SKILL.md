---
name: qa-check-definition-of-done
description: Use when a feature is at the final stage and the team must verify that review follow-ups, remaining tasks, and release-closeout work are finished.
---

# Check Definition of Done

Evaluate whether the feature is actually ready to close by checking review follow-ups, remaining work, and closure obligations against a reusable done-state checklist.

## Incoming artifacts

- Implementation plan and task status
- Any evidence about remaining work, PR state, docs, or task closure
- Jira issue should be available via Atlassian MCP

## Supporting files

- Read [checklist.md](checklist.md) as the done-state checklist template.

## Produce

Return a filled done-state checklist and a gaps summary for the caller to use in the feature workflow stage.

## Workflow

1. Read the done-state checklist.
2. Review the available implementation, review, and closure artifacts.
3. Using Atlassian MCP, try to read the Jira issue associated with the feature and check for open PRs or open sub-tasks.
4. Mark each checklist item as fully addressed, partially addressed, or not addressed.
5. Fill every `Answer:` line with evidence-based notes.
6. Summarize anything that still prevents closure.

## Output format

Use this structure:

```text
[x] ...
Answer: <what proves this is done>

[~] ...
Answer: Partial — <what is done>. Missing: <what remains>.

[ ] ...
Answer: Not addressed.
```

Append:

```text
Gaps summary:
- <every partial or missing item>
```

## Human checkpoints

- Any open item will be reviewed before the feature is declared done.
- If the human provides a confirmed closure plan, follow that plan and re-check afterward.

## Rules

- Use only the evidence provided to you.
- Do not assume PRs, subtasks, docs, or leftover work are complete unless the artifacts show that.
- Be explicit about what remains and why it still matters.
