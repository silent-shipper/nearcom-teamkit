---
name: what-did-i-get-done
description: Summarize authored commits over a user-specified time period into a concise status update.
---

# What Did I Get Done

Use this when the user needs a short, high-signal summary of work completed in a specific time range, such as yesterday, the last 3 days, last week, or a named date range.

## Workflow

1. Resolve the requested time window into concrete dates.
2. Read commits authored by the current git user email within that range.
3. Exclude merge commits and uncommitted changes.
4. Synthesize the most important shipped changes into a concise status update.
5. Include the actual date range used in the final summary.

## Commands

Find the author email:

```bash
git config user.email
```

Read commits in the range:

```bash
git log --no-merges --author="<email>" --since="<start>" --until="<end>" --oneline
```

If the summary needs more detail, inspect only the relevant commits:

```bash
git show --stat --oneline <sha>
git show --name-only --format=medium <sha>
```

## Guardrails

- Be concise and information-dense.
- Prioritize substantial behavior, architecture, reliability, or workflow changes.
- Omit cosmetic-only changes such as formatting, import sorting, and minor renames.
- Do not infer intent or motivation. Describe what changed functionally.
- Do not include uncommitted local work unless the user explicitly asks for it.

## Output

Return:

- one short summary suitable for a status update;
- the real date range used;
- optional 2-5 bullets for major changes only.
