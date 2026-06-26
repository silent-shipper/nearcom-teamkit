---
name: ship
description: Use when the user wants to commit, push, and open a PR. Also use when you hear "ship it", "push this", "open a PR", "create a PR", "commit and push", or when a feature/fix is complete and ready to ship.
---

# Ship

Lint, test, commit in logical pieces, push, open PR, and generate a concise testing checklist.

## Team Conventions (HARDCODED)

- **Never** add `Co-Authored-By` lines to commits
- **Never** use `git add -A` or `git add .` — stage specific files by name
- **Always** split changes into logical commits (one concern per commit)
- **Always** use conventional commit format: `type: description`
- **Always** run checks before committing

## Workflow

### Step 1 — Pre-flight Checks

Run these in parallel:

```bash
bun run check      # Biome lint
bun run typecheck  # TypeScript
bun run test       # Vitest
```

If any fail, fix the issues first. Do NOT skip checks.

### Step 2 — Analyze Changes

Run in parallel:
- `git status` (never use `-uall`)
- `git diff` + `git diff --staged`
- `git log --oneline -5` (for commit message style reference)

Review all changes. Group them into logical units — each unit gets its own commit.

**CRITICAL — Untracked File Check:**
After reviewing `git status`, look at untracked files (`??`). For each one, check if any modified/staged file imports it. CI runs on a fresh checkout — untracked files don't exist there. If committed code imports an untracked file, **it must be staged and committed or CI will fail.**

Quick check: `grep -r "from.*<untracked-file-stem>" src/ --include="*.ts" --include="*.tsx" -l`

### Step 2b — Sensitive Content & Relevance Scan

Before committing, scan all changed and untracked files for sensitive content and irrelevant artifacts.

**Sensitive content — block these from being committed:**
- API keys, tokens, secrets (patterns: `sk-`, `AKIA`, `ghp_`, `Bearer `, private keys)
- `.env` files, `credentials.json`, `*.pem`, `*.key`
- Hardcoded passwords or connection strings
- Session data, cookies, auth tokens in test fixtures

Quick scan:
```bash
git diff --cached --diff-filter=ACM --name-only | xargs grep -lEi '(sk-|AKIA|ghp_|private.key|password\s*=|secret\s*=|Bearer |-----BEGIN)' 2>/dev/null
git diff --diff-filter=ACM --name-only | xargs grep -lEi '(sk-|AKIA|ghp_|private.key|password\s*=|secret\s*=|Bearer |-----BEGIN)' 2>/dev/null
```

If any match: **STOP and warn the user.** Do not commit the file until confirmed safe.

**Irrelevant files — exclude from staging:**
- Debug logs, scratch files, local scripts not part of the feature
- Generated files (`.playwright-mcp/`, `node_modules/`, build output)
- Unrelated formatting changes or IDE config

For each changed file, ask: "Is this file part of the feature/fix being shipped?" If not, do not stage it. If unsure, ask the user.

### Step 3 — High-Value Tests

Before committing, evaluate whether any **extremely high-value** unit tests are missing for the changes being shipped. This is NOT "add tests for coverage." This is: "if this breaks in production, would a test have caught it?"

**Only add a test if it meets ALL of these criteria:**
1. Tests a non-obvious behavior that could silently regress (race conditions, edge cases in state machines, token type guard logic, amount parsing with decimals)
2. Would catch a real production bug, not just verify obvious code
3. Cannot be caught by TypeScript, lint, or manual QA alone
4. Tests the boundary/integration point, not internal implementation details

**Do NOT add tests that:**
- Verify a component renders (that's what eyes are for)
- Test simple getters/setters or trivial logic
- Duplicate what TypeScript already enforces
- Test framework behavior (XState transitions that are obvious from the machine definition)
- Exist just to make a coverage number go up

**If you identify 1-3 high-value tests:** Write them, commit separately as `test: description`.
**If nothing qualifies:** Skip this step entirely. Zero tests is better than filler tests.

Look at existing test patterns in the codebase (`*.test.ts` files near the changed code) and match the style. Use `vi.mock` for external deps, `fromPromise` for actor mocks, factory functions for test data.

### Step 4 — Commit in Logical Pieces

For each logical group:

1. Stage only the relevant files: `git add <specific-files>`
2. Commit with conventional format:

```bash
git commit -m "$(cat <<'EOF'
type: concise description of this logical change
EOF
)"
```

**Commit types:** `feat`, `fix`, `refactor`, `chore`, `test`, `docs`, `perf`

**Rules:**
- One concern per commit (e.g., separate "fix bug" from "add test for bug")
- Message describes the WHY, not the WHAT
- No `Co-Authored-By` — ever
- If unsure how to split, ask the user

### Step 5 — Pre-Push Verification

**STOP — run checks AGAIN after all commits are made.** Commits can change the codebase state (e.g., lint hooks rewrite files, cherry-picks resolve differently). Verify the final committed state is clean:

```bash
bun run typecheck  # Must pass — catches missing files, broken imports
bun run check      # Must pass — catches formatting drift from commit hooks
```

If either fails, fix and amend/add a commit before proceeding.

### Step 6 — Confirm Before Push

**STOP — this is a hard gate. Do NOT push without explicit user approval.**

Show the user:
1. The branch name
2. The commits that will be pushed: `git log --oneline origin/HEAD..HEAD` (or `git log --oneline -5` if no upstream yet)
3. A one-line summary of what changed

Then ask: **"Ready to push?"**

Wait for explicit approval ("go", "yes", "push it", "ship it", etc.) before running any push command.

**Why:** Pushing affects shared remote state, triggers CI, and notifies reviewers. It cannot be silently undone. The cost of one confirmation is zero. The cost of an unwanted push is high.

Once approved:

```bash
git push -u origin HEAD
```

If the branch doesn't have an upstream yet, this sets it. If it does, a regular push works.

### Step 7 — Open or Update PR

**First, check if a PR already exists for this branch:**

```bash
gh pr view --json url,title 2>/dev/null
```

**If a PR already exists:** Skip creation. Print the existing PR URL and move to Step 8.

**If no PR exists:** Create one:

```bash
gh pr create --title "type: concise title under 70 chars" --body "$(cat <<'EOF'
## Summary
- Bullet 1
- Bullet 2

## Test plan
- [ ] Test step 1
- [ ] Test step 2
- [ ] Test step 3
EOF
)"
```

**PR rules:**
- Title under 70 chars, conventional commit format
- Summary: 2-4 bullets max, what changed and why
- Test plan: 4-8 checkbox items, concise, covers happy path + key edge cases
- No verbose descriptions — the diff speaks for itself

### Step 8 — Output

Print:
1. The PR URL (existing or newly created)
2. The testing checklist (formatted for easy copy)

### Step 9 — Babysit CI

After outputting the PR URL, start a `/loop` to monitor CI until all checks pass (or fail definitively).

```
/loop 2m "Check CI status for the current PR. Run: gh pr checks --json name,state,conclusion | jq -r '.[] | \"\(.state) \(.conclusion // \"-\") \(.name)\"'

- If all checks show COMPLETED/SUCCESS: report success and stop the loop.
- If any check shows COMPLETED/FAILURE: report which check failed, fetch its logs with 'gh run view <run-id> --log-failed | tail -50', summarize the failure, and stop the loop.
- If checks are still PENDING/IN_PROGRESS: report status and continue waiting.
- If no checks found yet: continue waiting (CI may not have started)."
```

**Rules:**
- Max wait: ~20 minutes (10 iterations). If still pending after that, report and stop.
- On failure: show the failing check name + a concise summary of the error from logs. Then run `/loop cancel` to end the loop.
- On success: print a clear "CI passed" message. Then run `/loop cancel` to end the loop.
- **Always cancel the loop** when the stop condition is met (success or failure). The loop does not self-terminate — you must explicitly run `/loop cancel`.
- Do NOT attempt to fix CI failures automatically — report them and let the user decide.

## What This Is NOT

- Not a code review — don't suggest improvements, just ship what's there
- Not a rebase/squash tool — commit as logical pieces, don't rewrite history
- Not interactive — don't ask "should I commit this?" for each file. Group intelligently and go.

## If Something Fails

- **Lint fails:** Fix automatically if trivial (unused import, formatting). Otherwise tell the user.
- **Typecheck fails:** Tell the user. Don't guess at type fixes.
- **Tests fail:** Tell the user. Don't modify tests to make them pass.
- **Push fails:** Check if branch exists remotely. If conflict, tell the user.
- **PR creation fails:** Show the error. Common fix: branch not pushed yet.
