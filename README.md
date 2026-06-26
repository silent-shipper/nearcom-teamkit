# Nearcom Team Kit

Small set of skills for making team work sharper: clearer goals, cleaner architecture, stricter reviews, and safer shipping.

The daily drivers are `$grill-to-goal`, `$improve-codebase-architecture`, `$thermo-nuclear-code-quality-review`, and `$ship`. Use those constantly. The rest are utility skills for specific moments.

## Skills

| Skill | Use it when |
| --- | --- |
| `$karpathy-guidelines` | You are writing or changing code and want the agent to stay simple, surgical, and goal-driven. |
| `$grill-to-goal` | You have a plan, but it needs pressure testing before the agent runs with it. Good for turning fuzzy ideas into executable work. |
| `$grill-to-issue` | You want to turn an idea, bug, or decision into a clean GitHub issue. It grills first, then asks before using `gh api` to create or update anything. |
| `$improve-codebase-architecture` | You want architectural opportunities, not random cleanup. It looks for shallow modules, weak seams, and places where tests would get better if the model were deeper. |
| `$thermo-nuclear-code-quality-review` | You want a very strict maintainability review. Run it before merging anything. |
| `$orchestrator-pattern` | You want the orchestrator loop: triage tasks, delegate lanes, collect findings, and queue approval gates without taking external actions silently. |
| `$ship` | You are ready to commit, push, and open or update a PR with checks and a final approval gate before push. |
| `$what-did-i-get-done` | You need a short status update based on authored commits over a real date range. |

## Daily Drivers

These are the habits that should shape most work.

Use `$grill-to-goal` before a build when the shape is still soft. The point is to find the hard choices early, write down the vocabulary and ADRs as they crystallize, then hand the agent a tight goal.

Use `$improve-codebase-architecture` when the codebase shape is the question. It should find a few serious deepening opportunities, not a random cleanup list.

Use `$thermo-nuclear-code-quality-review` before merging anything. It is the quality gate for avoiding messy PRs, accidental spaghetti, giant files, weak abstractions, and clever code that should have been simple.

Use `$ship` when the work is ready to leave your machine. It stages specific files, checks the branch, commits in logical pieces, asks before push, and then opens or updates the PR.

## Utility Skills

Use these when the moment calls for them.

Use `$karpathy-guidelines` when coding starts to drift. It keeps the agent simple, surgical, and goal-driven.

Use `$grill-to-issue` when the work belongs in GitHub before it belongs in code. It should produce issues that a teammate can pick up without decoding a chat transcript.

Use `$orchestrator-pattern` when there is a queue. The root thread coordinates, workers inspect or implement inside their lane, and anything public or destructive waits for explicit approval.

Use `$what-did-i-get-done` for concise updates. Ask with a real window, like "yesterday", "last week", or "June 1 to June 7".

## Good default flow

```text
$grill-to-goal
  -> clarify the goal
  -> capture glossary / ADRs
  -> execute

$improve-codebase-architecture
  -> find the deeper shape
  -> pick the highest-leverage seam
  -> improve locality and testability

$thermo-nuclear-code-quality-review
  -> challenge the shape
  -> remove avoidable mess
  -> block merge until the structure is good

$ship
  -> checks
  -> logical commits
  -> push approval
  -> PR
```

For larger queues:

```text
$orchestrator-pattern
  -> triage
  -> delegate lanes
  -> synthesize
  -> queue decisions
  -> execute only approved actions
```

## Team rule

The useful output is not more agent activity. The useful output is fewer vague tasks, fewer messy PRs, fewer hidden decisions, and more work that is ready for another teammate to trust.

Default to grilling before building. Look for the better architecture before adding more code. Run thermo-nuclear review before merging anything. Ship only when the branch is clean, checked, and understandable.
