---
name: qa-check-definition-of-ready
description: Use when the workflow stage needs a checklist-based readiness review of the current task description, scope, and acceptance information.
---

# Check Definition of Ready

Evaluate the produced Kick-off artifacts together and report what is covered, partially covered, or still missing before the workflow moves into Focus-stage planning.

## Incoming artifacts

- Business idea / task requirements document
- Implementation scope description
- Acceptance criteria or equivalent outcome checks
- Any additional kickoff context that the caller explicitly includes

## Supporting files

- Read [checklist.md](checklist.md) as the checklist template.

## Produce

Return a filled kickoff checklist plus a gaps summary for the caller to store as the readiness artifact.

## Workflow

1. Read the checklist template.
2. Read all supplied Kick-off artifacts together before scoring readiness.
3. Evaluate each checklist item only against the supplied Kick-off artifacts.
4. Mark every item as:
   - `[x]` fully addressed
   - `[~]` partially addressed
   - `[ ]` not addressed
5. Replace every `Answer:` line with a concise answer grounded in the source text.
6. Append a `Gaps summary` listing every partial or missing item.
7. Assess overall readiness rate (in percentage) by dividing the number of fully addressed items by the total number of checklist items and multiplying by 100.

## Output format

Use this structure for every checklist line:

```text
[x] 1.1 ...
Answer: <concise answer from source>

[~] 5.3 ...
Answer: Partial — <what is present>. Missing: <what is absent>.

[ ] 11.2 ...
Answer: Not addressed.

Readiness: <calculated percentage>
```

## Human checkpoints

- Run this check after the Kick-off stage has already produced both the business-idea and implementation-scope artifacts.
- Partial and missing checklist items should be reviewed before the Focus stage starts.
- If any Kick-off artifact changes after discussion, the checklist should be re-run.

## Rules

- Use only facts explicitly present in the supplied Kick-off artifacts.
- Do not invent, infer, interpolate, or silently "fill in" missing details.
- If wording is ambiguous, mark the item partial and explain the ambiguity.
- For items 6.3 and 6.4, judge against the "Broader Context & Parent Activity" section of the business-idea artifact: flag a missing or undescribed broader context as a gap, and flag a missing parent issue as a **blocker** for any issue type except Bug (a Bug needs no parent — mark 6.4 `N/A — Bug`).
- Keep answers concise and faithful to the source.
- Focus on readiness for the Focus stage, not on general release, rollout, or support obligations.
