---
name: pm-prepare-task-requirements
description: Use when the workflow stage needs a business idea summary and acceptance criteria derived strictly from the request and available documentation.
---

# Prepare Task Requirements

Turn a task description, business idea, or Jira issue into a structured business summary plus acceptance criteria for the feature-development workflow.

## Incoming artifacts

- User-provided task description, business idea, or Jira issue
- Attached files or linked documentation
- Product documentation that the PM agent is allowed to consult
- For a Jira issue: its parent and linked issues (and any Confluence pages they reference), read via the Atlassian MCP to establish broader context

## Produce

Return a draft that the caller can save as the business-idea artifact. Do not create workflow files yourself.

Use this structure:

```markdown
# Business Idea: <title>

## Problem Statement
## Business Value (Why)
## Broader Context & Parent Activity
## Stakeholders and Users
## Expected Outcome / Proposed Solution (What)
## Scope (what is included and what is out of scope)
## Known risks

# Acceptance Criteria: <title>

## Summary
## Acceptance Criteria
- [ ] AC1: ...
- [ ] AC2: ...
```

## Workflow

1. Read the incoming request and supporting material completely.
2. Identify the problem being solved, the affected audience, and the expected outcome.
3. Discover the broader context. A task is usually part of a bigger activity. When working from a Jira issue, use the Atlassian MCP to follow its parent and linked issues (and any Confluence pages they reference) and establish the larger initiative this task belongs to. Capture this under `## Broader Context & Parent Activity`: the parent issue key/link, the initiative it serves, relevant linked issues, and how this task fits. If no broader context or parent issue can be established, state that explicitly — do not invent one.
4. Summarize the business idea in product language: what, why, and for whom.
5. Derive clear, testable acceptance criteria from the supplied information - how the feature will be validated.
6. Call out any missing or ambiguous requirement as an explicit open question instead of filling it in yourself.

## Human checkpoints

- The business summary will be shown to the human before downstream planning starts.
- Any open question or missing requirement will be resolved by the human or by source documents before later stages depend on it.

## Rules

- Use only facts present in the request, attachments, approved documentation, and the Jira issue with its parent/linked issues you read via the Atlassian MCP.
- Do not invent requirements, edge cases, user roles, rollout assumptions, or metrics.
- Keep the acceptance criteria concrete and testable.
- Do not decide implementation details unless the source material explicitly requires them.
- Do not implement any code.
