---
name: standup
description: /standup [project|all] — cross-repo status: reads the _PROJECTS roster + git state across repos and reports what changed, what's stale, and what's next by priority
usage: /standup [<project> | all]
examples:
  - /standup
  - /standup all
  - /standup frunk
allowed-tools:
  - Bash(git:*)
  - Bash(gh:*)
  - Bash(ls:*)
  - Read
  - Glob
  - Grep
---

# Standup (cross-repo status)

A daily-style digest across the whole app roster so the entire portfolio keeps moving — not just whatever's already open. **Read-only.**

## Inputs
- `~/Dev/_PROJECTS/README.md` — roster, the **Next up** priority order, and stacks.
- Repos under `~/Dev/*-proj/` (plus `~/Dev/mm-claude-starter`, `~/OSS/*`).
- Scope: no arg or `all` → the **Next up** list plus anything with recent activity; `<project>` → just that one.

## Gather (per project, read-only)
- **Recent activity:** `git -C <repo> log --oneline -5 --since='14 days ago'`; last-commit date.
- **Working state:** dirty/clean (`git status --porcelain`), current branch, ahead/behind origin.
- **Open PRs / stale branches:** `gh pr list`; old feature branches.
- **From the roster:** % done, active/paused/defunct, stack.

## Report
Concise, ordered by the **Next up** priority — a glance, not an essay:

```
STANDUP — <date>

▲ Next up
1. no-dinos.com    ~95%  · last commit 3d ago · clean · 2 images left → v1.0
2. retireat55.club ~25%  · last commit 9d ago · feat/calc dirty · (unnamed)
...

⏸ Stale (no activity >30d): frunk · diamondheart · sidvid
⚠ Needs you: hoobie (live DB down) · markmcdermott.io (resume TODO)
```

For each top item, end with **one concrete next action** — the smallest thing that moves it.

## Notes
- Pure read-only — never commit, push, or modify anything.
- Schedulable: pair with `/schedule` for a standup each morning.
- Convert any relative dates to absolute when reporting.
