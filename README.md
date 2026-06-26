# Nearcom Team Kit

This is a small skill pack for how we want agent work to feel on the team.

The point is not to make the agent do more. The point is to make the work less vague, less messy, and easier for another person to trust.

Default habit:

```text
$karpathy-guidelines
  -> keep the agent simple, surgical, and focused on the root problem

$grill-to-goal
  -> make the goal sharp before building

$improve-codebase-architecture
  -> check whether the current shape is the right shape

$thermo-nuclear-code-quality-review
  -> make the PR as small and easy to review as possible before merge

$ship
  -> commit, push, and PR only when the branch is clean
```

That is the main loop.

Keep the agent on track from the start. Grill first when the work is fuzzy. Improve the architecture when the shape feels wrong or too shallow. Always run thermo-nuclear review before merge so the PR is as minimal and easy to review as possible. Ship only after the work is checked and understandable.

For bigger queues, use the orchestrator pattern:

```text
$orchestrator-pattern
  -> triage
  -> delegate focused lanes
  -> synthesize
  -> queue decisions
  -> execute only approved actions
```

The root thread should coordinate. Workers can inspect or implement. Anything public, destructive, or shared needs explicit approval.

## Skills

### Daily drivers

| Skill | Use it when |
| --- | --- |
| `$karpathy-guidelines` | The agent needs to stay simple, surgical, and focused on the root problem. |
| `$grill-to-goal` | The idea is not sharp enough yet. Use it to pressure test the plan, capture decisions, and turn it into a clear goal. |
| `$improve-codebase-architecture` | The codebase shape matters. Use it to find deeper modules, better seams, and cleaner places for behavior to live. |
| `$thermo-nuclear-code-quality-review` | Before merging anything. Use it to make sure the PR is as small and easy to review as possible while still catching messy structure, bad abstractions, giant files, and avoidable complexity. |
| `$ship` | The work is ready to leave your machine. Use it for checks, logical commits, push approval, and PR creation. |

### Utilities

| Skill | Use it when |
| --- | --- |
| `$grill-to-issue` | The work should become a GitHub issue before it becomes code. |
| `$orchestrator-pattern` | There is a queue of tasks, reviews, approvals, or worker lanes to coordinate. |
| `$what-did-i-get-done` | You need a short status update from real commits over a real date range. |

## Team rule

If the output creates more ambiguity, it failed.

Good agent work should leave behind a sharper goal, a cleaner structure, a stricter review, or a branch that is smaller and easier to ship.
