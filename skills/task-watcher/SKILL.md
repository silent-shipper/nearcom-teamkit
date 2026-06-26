---
name: task-watcher
description: Orchestrated watcher loop for processing queued tasks with worker threads and a human-input queue. Use when asked to watch tasks, triage items, delegate analysis, or collect approval gates.
---

# Task Watcher

Run a simple orchestrator loop. The root thread coordinates only. Workers inspect or implement inside their assigned lane. Any external action waits for human approval.

This is a control-plane skill: load state, delegate, synthesize, queue decisions, and report. Do not let the coordinator become the implementation thread unless the task is too small to justify a worker.

## Empty Invocation Menu

If the user invokes `$task-watcher` without a target, show:

1. Watch queue
   Find eligible tasks and process them one by one.

2. Review one task
   Inspect a specific task and prepare a recommendation.

3. Babysit my tasks
   Watch my tasks for new feedback and queue replies or fixes.

4. Take over task
   Make an approved local fix, verify it, then queue a push/update.

Ask which mode to run.

## Authorization Model

Treat each permission separately:

- triage and local inspection;
- worker delegation;
- local implementation;
- public comment or issue update;
- push or PR update;
- close, merge, release, or destructive action.

Do not infer one permission from another. Queue the exact next action and wait for approval.

## Core Loop

For each task:

1. Load task state.
2. Check eligibility.
3. Spawn workers for independent lanes when useful.
4. Collect findings.
5. Synthesize a recommendation.
6. Queue human input.
7. Continue other safe work.
8. Execute only approved actions.

Smallest concrete state machine:

```text
Find task
  -> Load state
  -> Delegate workers
  -> Synthesize
  -> Queue decision
  -> User approves?
       yes -> execute action -> record result
       no  -> skip/revise
  -> Next task
```

## Loading Task State

For GitHub tasks, use `gh api` or `gh pr/issue view` depending on the local convention. Prefer API reads when the next action might become an API mutation.

Capture:

- canonical URL;
- title and current state;
- author and latest human feedback;
- current version, such as issue updated time, PR head SHA, or branch ref;
- labels, assignees, milestone, and CI state when relevant;
- local repo status before implementation work.

Never report only `#123`. Use full canonical URLs in user-facing reports.

## Eligibility

Classify each task:

- `autonomous`: clear scope, bounded implementation, reproducible, verifiable locally.
- `needs human`: product choice, security/privacy decision, credential, live proof access, destructive action, or ambiguous acceptance criteria.
- `watch only`: waiting for another actor, CI, review, or external event.
- `ignored by user`: only when the user explicitly says to ignore it.

Do not treat stale, hard, draft, or noisy tasks as ignored.

## Worker Lanes

Use workers when useful and available. If worker tooling is unavailable, run the lanes serially and say so.

1. Correctness lane
   Look for real behavior bugs, root cause, missing checks, and reproduction paths.

2. Maintainability lane
   Look for unnecessary complexity, bad ownership, shallow modules, and avoidable branch growth.

3. Risk lane
   Look for security, data loss, operational risk, stale state, release risk, and missing proof.

Worker prompt shape:

```text
You are a worker for <lane>.
Task: <task id / URL>
Allowed: inspect only unless the root prompt explicitly grants implementation.
Forbidden: external actions, pushes, comments, approvals, subworkers.
Return: findings, evidence, impact, minimal remedy, confidence.
```

Workers must not create subworkers or manage other threads.

## Synthesis Bar

Promote only findings that are:

- evidence-backed;
- material;
- actionable;
- tied to the current task.

Drop style nits and speculative concerns.

For review-like tasks, answer:

```text
Ref:
Surface:
Problem:
Cause:
Best fix:
Proof:
Risk:
Recommendation:
```

If the root cause is not proven, say what evidence is missing.

## Human Input Queue

Maintain a queue of decisions:

```text
id:
task:
current_version:
requested_action:
proposal:
proof:
risk:
status: pending | approved | rejected | superseded | executed
```

Rules:

- Never execute a queued action without explicit approval.
- If the task changes, mark queued items stale or superseded.
- Let the user approve or reject multiple items in one reply.
- Keep working on other safe tasks while waiting.
- Refresh task state immediately before asking for approval and immediately before execution.

## Decision Briefs

Never ask the user to approve from only a URL or vague status.

Every approval request must include:

- full canonical URL and title;
- what changes and who benefits;
- why the decision is needed now;
- proof already completed;
- residual risk or missing evidence;
- recommendation with rationale;
- exact choices and what each choice does.

The normal choices should be concrete: land this prepared PR, post this exact reply, update this issue body, provide this exact access, or reject/close with this reason.

## Feedback Babysitting

For new human feedback:

1. Classify it:
   - relevant
   - not relevant
   - already fixed
   - duplicate
   - needs clarification

2. Queue one action:
   - relevant: minimal fix plan
   - not relevant, already fixed, duplicate, unclear: proposed reply

Do not reply or fix without approval.

## Takeover Mode

When the user approves local implementation:

1. Refresh the task.
2. Read repo instructions and local docs.
3. Reproduce or identify the root cause when possible.
4. Make the smallest correct local change.
5. Run focused checks.
6. Run broader checks when the blast radius warrants it.
7. Queue any push, PR update, public comment, close, or merge.

No public mutation is implied by local implementation approval.

## Output Shape

```text
Active:
- <task>: <state>

Human input queue:
1. <task> - <requested action>
   Proposal: <exact action>
   Proof: <short evidence>
   Risk: <short risk>

Next:
- <what the watcher is doing now>
```

Report meaningful changes, not routine polling.
