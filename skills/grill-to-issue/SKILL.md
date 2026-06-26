---
name: grill-to-issue
description: Grill an idea, bug, or plan into a precise GitHub issue, then use gh api to create, edit, comment on, or update the issue only after explicit approval.
---

# Grill To Issue

Turn fuzzy work into a GitHub issue another teammate can act on. Three phases, in order:
**grill -> draft -> approved GitHub mutation**.

This skill is for issue creation and issue updates. It is not for silently filing tickets from half-understood chat.

## Hard Rules

- Ask one question at a time.
- If the answer is discoverable from the repo, inspect the repo instead of asking.
- Use `gh api` for GitHub mutations.
- Never create, edit, comment, close, reopen, label, assign, or milestone an issue without explicit user approval for that exact action.
- Before any GitHub mutation, show the exact title/body/comment/fields that will be sent.
- If an existing issue changed since the draft was prepared, mark the queued action stale and refresh before asking again.
- Prefer a clear issue over a long issue.

## Empty Invocation Menu

If the user invokes `$grill-to-issue` without a target, show:

1. Create issue
   Grill an idea or bug into a new issue.

2. Improve existing issue
   Read an issue and prepare a clearer body, labels, or next action.

3. Add issue update
   Prepare a comment with new findings, proof, or a proposed decision.

4. Close or reopen issue
   Prepare a state change with proof and ask for approval.

Ask which mode to run.

## Phase 1: Grill

Clarify the smallest useful issue. Walk the design tree until the work is actionable.

Recommended first question by mode:

- bug: "What exact behavior is wrong, and what should happen instead?"
- feature: "Who needs this, and what is the smallest useful version?"
- refactor: "What pain does this remove, and how will we know it worked?"
- decision: "What choice needs to be made, and what options are still alive?"

For each question:

- provide the recommended answer when you can infer one;
- ask for confirmation or correction;
- update the draft as the answer crystallizes.

## What To Inspect

Before asking questions that code can answer, inspect:

- existing issues and PRs for duplicates or prior decisions;
- relevant README, docs, and local instructions;
- tests around the affected behavior;
- owner modules and call paths around the affected behavior;
- `CONTEXT.md` and `docs/adr/` when present.

For GitHub issue reads, prefer:

```bash
gh api repos/OWNER/REPO/issues/NUMBER
gh api repos/OWNER/REPO/issues/NUMBER/comments
```

If the repo owner/name is unknown:

```bash
gh repo view --json nameWithOwner,url
```

## Phase 2: Draft

Build the issue in this shape:

```markdown
## Problem

What is wrong or missing.

## Desired outcome

What done looks like.

## Context

Relevant constraints, prior decisions, links, or code pointers.

## Scope

What is included.

## Non-goals

What is explicitly out.

## Acceptance criteria

- [ ] Checkable result 1
- [ ] Checkable result 2

## Proof plan

How a teammate should verify it.
```

Keep sections that matter. Delete empty sections instead of filling them with noise.

## Phase 3: Queue Approval

Before mutating GitHub, queue a decision:

```text
Issue action:
repo:
target:
current_version:
requested_action:
title:
body_or_comment:
labels:
assignees:
milestone:
risk:
command:
status: pending
```

Then ask:

```text
Create/update this issue? (go / edit)
```

Only `go`, `yes`, `create it`, `update it`, or an equally explicit approval executes the mutation.

## GitHub Mutations

Create an issue:

```bash
gh api repos/OWNER/REPO/issues \
  -f title='...' \
  -f body='...'
```

Update an issue:

```bash
gh api -X PATCH repos/OWNER/REPO/issues/NUMBER \
  -f title='...' \
  -f body='...'
```

Add a comment:

```bash
gh api repos/OWNER/REPO/issues/NUMBER/comments \
  -f body='...'
```

Close or reopen:

```bash
gh api -X PATCH repos/OWNER/REPO/issues/NUMBER \
  -f state='closed'
```

Use fields such as labels, assignees, milestone, or state only when the user approved those exact changes.

## Output

After execution, report:

```text
Issue:
Action:
Result:
Next:
```

If approval is pending, report the queued action and keep working only on safe local inspection.
