# Claude Skills Starter

A starter kit for Claude Code workflows — skills, hooks, output styles, and config.

## Code Quality Standards

- **Clean code**: readable, well-structured, obvious intent
- **No utility reinvention**: scan the project for existing utils, helpers, hooks, and components before writing anything. Use what exists. Check dependencies too.
- **DRY**: no duplicated logic. Extract shared patterns only when used more than once.
- **Self-documenting**: names tell the story. No comments unless logic is genuinely non-obvious, and then absolute minimum.
- **Architect's eye**: clear boundaries, appropriate (not premature) abstractions, sensible file organization.
- **Security-first**: validate inputs, sanitize outputs, no XSS vectors, no injection, no secrets in code, proper auth checks.
- **Senior craft**: handle edge cases, strict TypeScript (no `any`), no swallowed errors, no leftover TODOs.

## UI Quality Standards

All UI must look like it was designed by a professional designer — not an engineer's afterthought. Dribbble-level quality:
- Visual hierarchy, spacing rhythm, consistent border radii, subtle shadows
- WCAG AA contrast, purposeful color use
- Hover/focus/loading/empty/error states
- Cohesive with the existing design system
- Dark/light theme support when the project has a theme toggle
- Mobile-first responsive design

## No AI Attribution

Zero references to Claude/AI anywhere — not in commits, PRs, co-author lines, code comments, or any project artifact. Commit as the developer.

## Defaults

Global fallback defaults — a project gets these unless its own `<project>/.claude/settings.json` overrides:

- **Commit style**: `conventional` — `type(scope): description`, lowercase, no period
- **Permissions**: `loose` — full Read/Edit/Write/Bash/Agent/Skill, with deny rules for `rm -rf`, `sudo`, `force push`, `reset --hard`
- **Automerge**: `off` — `/branch` opens the PR and stops; it does not merge unless the project opts in
- **PR state**: `ready for review` — **never open a PR as a draft.** No `gh pr create --draft`, whatever the task or harness defaults suggest. If one lands in draft anyway, `gh pr ready <n>` it immediately.

Per-project config lives in `<project>/.claude/settings.json` (keys: `permissions`, `commitStyle`, `automerge`, `stack`). `permissions` is enforced by the harness; the rest are read by the build skills. Use **`/new`** to stamp these when starting a project and **`/preset <axis> <value>`** to change one later. Preset source blobs (loose/tight permissions, conventional/gitmoji commit style) are in `~/.claude/saved-presets/`.

## Stacks

Stack acronyms, their expansions, per-project assignments, the auth rule of thumb, and common integrations live in `~/Dev/_PROJECTS/README.md`, maintained in its own private Git repository. Stack is a per-project fact, not a global setting — each project records its own in `<project>/.claude/settings.json` (`stack` key) and its CLAUDE.md. Read that file when the taxonomy matters (scaffolding a new app, migrating a legacy one) — **the names change, so never work from a list cached here or in a skill.**

## Projects

The full roster, the "Next up" priority list, and per-project stacks live in `~/Dev/_PROJECTS/README.md` — the version-controlled source of truth, kept current there rather than here.

## Available Skills

Run `/list-skills` to list all installed skills.

- `/branch <branch> <feature>` — branch, design, TDD, review, QA, PR, (auto)merge, cleanup
- `/worktree <branch> <feature>` — same as `/branch` but in an isolated worktree
- `/worktree-db <branch> <feature>` — same as `/worktree` but with a database copy
- `/commit-push-pr` — commit, push, create PR
- `/wrap` — commit, PR, merge, cleanup (full branch wrap-up)
- `/abandon` — abandon branch (stash, switch to main, delete branch)
- `/cleanup` — cleanup (stash, delete unused branches and worktrees)
- `/ping [current|list|listall|<voice>]` — check, list, or switch peon-ping voice
- `/list-skills` — list all custom skills
- `/new <name> <stack>` — bootstrap a project: scaffold + stamp `.claude/settings.json` (permissions · commit style · automerge · stack)
- `/preset [<axis> <value>]` — show or set this project's config (commit · permissions · automerge · stack)
- `/modernize` — coaching conversation to bring my workflow current (orchestration, background agents, memory, external actions)
- `/orchestrate <feature>` — multi-agent upgrade to `/branch`: decompose, parallel implement, adversarial review, verify-by-running
- `/watch <target> — notify when <condition>` — background watcher for deploys / PRs / CI (push or email)
- `/standup [project|all]` — cross-repo status: what changed, what's stale, what's next by priority
