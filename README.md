# Nearcom Team Kit

Small set of skills for making team work sharper: better plans, better issues, better reviews, cleaner architecture, and safer shipping.

## Skills

| Skill | Use it when |
| --- | --- |
| `$karpathy-guidelines` | You are writing or changing code and want the agent to stay simple, surgical, and goal-driven. |
| `$grill-to-goal` | You have a plan, but it needs pressure testing before the agent runs with it. Good for turning fuzzy ideas into executable work. |
| `$grill-to-issue` | You want to turn an idea, bug, or decision into a clean GitHub issue. It grills first, then asks before using `gh api` to create or update anything. |
| `$improve-codebase-architecture` | You want architectural opportunities, not random cleanup. It looks for shallow modules, weak seams, and places where tests would get better if the model were deeper. |
| `$thermo-nuclear-code-quality-review` | You want a very strict maintainability review. Use it before landing big changes or when a PR feels like it is growing messy. |
| `$task-watcher` | You want an orchestrator loop: triage tasks, delegate lanes, collect findings, and queue approval gates without taking external actions silently. |
| `$ship` | You are ready to commit, push, and open or update a PR with checks and a final approval gate before push. |
| `$what-did-i-get-done` | You need a short status update based on authored commits over a real date range. |

## How to use it well

Start with the smallest skill that fits the moment.

Use `$karpathy-guidelines` by default when coding. It keeps the agent from adding cleverness you did not ask for.

Use `$grill-to-goal` before a build when the shape is still soft. The point is to find the hard choices early, write down the vocabulary and ADRs as they crystallize, then hand the agent a tight goal.

Use `$grill-to-issue` when the work belongs in GitHub before it belongs in code. It should produce issues that a teammate can pick up without decoding a chat transcript.

Use `$improve-codebase-architecture` when the question is about the codebase shape. It should produce a few serious opportunities, not a laundry list.

Use `$thermo-nuclear-code-quality-review` when the implementation quality bar matters. It should push for simpler structure, fewer branches, clearer ownership, and less file sprawl.

Use `$task-watcher` when there is a queue. The root thread coordinates, workers inspect or implement inside their lane, and anything public or destructive waits for explicit approval.

Use `$ship` only when you mean it. It stages specific files, checks the branch, commits in logical pieces, asks before push, and then opens or updates the PR.

Use `$what-did-i-get-done` for concise updates. Ask with a real window, like "yesterday", "last week", or "June 1 to June 7".

## Good default flow

```text
$grill-to-goal
  -> clarify the goal
  -> capture glossary / ADRs
  -> execute

$thermo-nuclear-code-quality-review
  -> challenge the shape
  -> remove avoidable mess

$ship
  -> checks
  -> logical commits
  -> push approval
  -> PR
```

For larger queues:

```text
$task-watcher
  -> triage
  -> delegate lanes
  -> synthesize
  -> queue decisions
  -> execute only approved actions
```

## Team rule

The useful output is not more agent activity. The useful output is fewer vague tasks, fewer messy PRs, fewer hidden decisions, and more work that is ready for another teammate to trust.
