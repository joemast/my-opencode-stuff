---
name: dev-code-review
description: Use when implemented changes need a developer-oriented review against the implementation plan, changed code, and obvious engineering risks.
---

# Developer Code Review

Review an implementation as a reviewer, not as the implementer. Focus on correctness, scope alignment, maintainability risks, and missing coverage.

## Incoming artifacts

- Final implementation plan
- Code changes, diff, branch, or pull request usually available via Git
- Any relevant test or check output

## Produce

Return a developer review report for the caller to use in the Review stage.

## Invocation note

Use this structure:

```markdown
# Developer Review: <title>

## Summary
## Blocking Findings
- ...
## Important Findings
- ...
## Nice-to-Have Improvements
- ...
## Verdict
```

## Workflow

1. Read the implementation plan and the changed code together.
2. Check that the delivered code matches the intended plan or completed tasks.
3. Look for correctness issues, risky assumptions, missing tests, or scope drift.
4. Verify that the code adheres to general coding best practices and project's guidelines (specified in the github-instructions).
5. Separate blocking issues from improvements.
6. Return a concise review that the human or workflow can act on.

## Human checkpoints

- If the review depends on missing evidence, ask for that evidence instead of guessing.

## Rules

- Stay review-oriented; do not implement fixes inside the review.
- Prefer concrete, actionable findings over generic style commentary.
- Do not request unrelated refactors.
- Compare the result against the implementation plan, not against imagined future work.
- Prefer an implementation-independent reviewer configuration when the caller can choose the model or agent.
