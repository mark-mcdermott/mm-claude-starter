---
name: branch
description: /branch <branch> <feature> — branch, design, TDD, review, QA, PR, (auto)merge, cleanup
usage: /branch <branch> <feature>
examples:
  - /branch login-fix fix the login redirect loop on expired sessions
  - /branch merch-store add a merch store page with product grid and Stripe checkout
  - /branch add a dark mode toggle to the settings panel
allowed-tools:
  - Bash(npx:*)
  - Bash(git:*)
  - Bash(gh:*)
  - Bash(npm:*)
  - Bash(node:*)
  - Read
  - Edit
  - Write
  - Glob
  - Grep
  - Agent
  - TaskCreate
  - TaskUpdate
  - TaskGet
  - WebFetch
---

# Branch and Build Skill

Create a branch, design, implement (TDD), review, QA, and open a PR — fully autonomous. Whether it then merges and cleans up depends on the project's `automerge` setting (default off). **Never ask the user for details or confirmation.** Assess available options and choose the best one.

## Arguments

- **First word**: Branch name (optional). If it looks like a slug (`login-fix`, `feat/dark-mode`), use it as the branch name. If the entire input reads as a natural-language description, generate a short kebab-case branch name from it.
- **Remaining words**: Feature, refactor, or fix to implement.
- If no arguments at all, stop and ask what to build (the one exception to "never ask").

## Implementation Rules

These rules apply to ALL code written during this skill. Re-read after context compression.

1. **Clean code** — readable, well-structured, obvious intent
2. **No utility reinvention** — scan the project for existing utils, helpers, hooks, and components BEFORE writing anything. Use what exists. Check project dependencies too.
3. **DRY** — no duplicated logic. Extract shared patterns only when used more than once.
4. **Self-documenting** — names tell the story. No comments unless logic is genuinely non-obvious, and then absolute minimum.
5. **Architect's eye** — clear boundaries, appropriate (not premature) abstractions, sensible file organization, as if designed by a careful senior architect.
6. **Security-first** — validate inputs, sanitize outputs, no XSS vectors, no injection, no secrets in code, proper auth checks, CSRF protection. As if collaborated on with a careful senior security engineer.
7. **Senior craft** — handle edge cases, strict TypeScript (no `any`), no swallowed errors, no leftover TODOs, as if written by a careful senior developer.

## Workflow

### 1. Create Branch

```bash
git status
```
If dirty, stash first: `git stash push -m "branch: auto-stash before branching"`.

Infer the appropriate prefix from the feature description if the branch name has no prefix:
- New functionality → `feat/`
- Bug fix → `fix/`
- Restructuring → `refactor/`

```bash
git checkout -b <prefix>/<branch-name>
```

### 2. Explore & Prepare

Before writing any code:

1. **Scan the codebase** — understand existing patterns, utilities, components, helpers, styles, test structure, and conventions. Note:
   - Shared utility functions and libraries already in the project
   - Existing UI components, their patterns and styles
   - Directory structure and file organization conventions
   - Test patterns and conventions
   - `package.json` scripts and dependencies

2. **Never recreate** what already exists in the project or its dependencies.

### 3. Design (UI Features)

If the feature involves any UI elements:

**Think like a senior designer whose portfolio belongs on Dribbble.** Before writing code, design the interface:

- **Layout** — visual hierarchy, spacing rhythm, alignment grids, responsive breakpoints (320px → 768px → 1024px → 1440px)
- **Typography** — font sizes, weights, line heights that create clear hierarchy
- **Color** — work within the existing palette, WCAG AA contrast, purposeful not decorative
- **Interaction** — hover states, focus rings, transitions, loading states, empty states, error states
- **Polish** — consistent border radii, subtle shadows, micro-animations that feel intentional
- **Cohesion** — every element must feel native to the existing design system
- **Theme** — if the project has a dark/light toggle, all new UI must work in both themes

The UI must look like it was designed by a professional designer — not like a junior art project or an engineer's afterthought.

### 4. Plan

If the feature is complex (multiple files, sub-features, frontend + backend):

1. Create a numbered checklist of steps ordered by dependency
2. Use `TaskCreate` for each step
3. Write the checklist to `.claude/branch-progress.md` (survives context compression)

Simple features (single component, single fix) — skip the checklist, implement directly.

### 5. TDD Cycle

For each feature or checklist step:

**Re-read `.claude/branch-progress.md` before each step** (essential after context compression).

#### a. Write Tests

Match the project's existing test setup. Default split for the Vite/React stacks (RATS · REST · RAVEHANDS · ZENCATS) and Next (TANS):
- **Vitest** — unit & component logic (functions, hooks, components via Testing Library)
- **Playwright** — end-to-end flows (real browser, user journeys)

Write whichever fits the change (often both), covering:
- Happy path(s)
- Edge cases (empty states, error states, boundary values)
- Accessibility where relevant (keyboard nav, screen reader labels)

Follow existing test conventions and place tests where the project's tests live. Prefer the project's own `package.json` scripts when present — e.g. `npm run test` (Vitest), `npm run test:e2e` (Playwright) — else fall back to `npx vitest run` / `npx playwright test`.

#### b. Confirm Failure

Run the new tests. They **must fail** — if they pass, they're not testing anything new, so revise. Confirm they fail for the right reason (missing feature, not a syntax error).

#### c. Implement

Build the feature following the **Implementation Rules** above.

#### d. Run Tests & Fix

Re-run the new tests:
- **Pass** → continue
- **Fail** → fix the implementation (never weaken the test), rerun. Cycle until green.

#### e. Full Suite Check

Run the full suite (unit + e2e). Fix any regressions before moving on.

#### f. Commit

**Commit style**: Read `commitStyle` from `<project>/.claude/settings.json` (`conventional` if absent), then read its format from `~/.claude/saved-presets/commit-style-<style>.md`.

```bash
git add <relevant files>
git commit -m "<message following commit style>"
```

**CRITICAL**: Zero AI/Claude attribution in commit messages, co-author lines, or any commit metadata whatsoever.

Commit at every natural boundary — after each passing feature, each checklist step, each meaningful refactor.

#### g. Update Progress

If using a checklist, update `.claude/branch-progress.md` and mark the `TaskUpdate` as completed.

### 6. Security Review

After all features are implemented and tests pass:

**Think like a senior security engineer.** Review every changed file for:

- Input validation gaps (user input, API params, URL params, form data)
- XSS vectors (unsanitized rendering, innerHTML, dangerouslySetInnerHTML)
- SQL injection (raw queries, string interpolation)
- Auth/authz bypasses
- Sensitive data exposure (tokens, keys, PII in logs or responses)
- CSRF vulnerabilities
- Insecure dependencies or patterns
- Race conditions
- Path traversal

Issues found → fix → re-review. Cycle until clean.

### 7. Code Review

**Think like a senior developer reviewing a colleague's PR.** Review every changed file for:

- Logic errors or incorrect assumptions
- Missing error handling at system boundaries
- Performance issues (N+1 queries, unnecessary re-renders, unbounded loops)
- Type safety gaps
- Dead code or unused imports
- Inconsistency with existing codebase patterns
- Overly complex code that could be simpler

Issues found → fix → repeat both security review and code review. Cycle until both pass clean.

### 8. QA Cycle

#### a. Write QA Instructions

Write clear, step-by-step testing instructions:
- Happy path test cases with expected outcomes
- Edge cases to verify
- What "correct" looks like for each case

#### b. Execute QA

**Think like a meticulous QA engineer.** Follow the instructions:

- Test every happy path
- Search for and test edge cases beyond what was listed
- Verify error states, empty states, loading states
- Test with varied data scenarios

#### c. Regression Check

Look at all areas the code may have touched:
- Navigate adjacent pages/routes
- Test features sharing components or utilities with the new code
- Check for visual regressions (layout shifts, broken styles)
- Run the full test suite one more time

Issues found → fix → re-QA. Cycle until clean.

### 9. Finalize

#### a. Slop Check

Final review for AI coding artifacts:
- Files created but never imported
- Single-use abstractions (inline them)
- Utility files duplicating library functionality
- Over-engineered patterns for simple things
- Gratuitous comments or docstrings on self-evident code

Fix anything found.

#### b. Update README

Review the project's `README.md`. Make appropriate updates:
- New features, setup instructions, env vars, commands
- Remove outdated information
- Skip if no README or the change doesn't warrant mention

#### c. Final Commit

Commit any remaining changes from reviews/QA/slop-check/README.

#### d. Create PR

Open it **ready for review** — never `--draft`.

```bash
git push -u origin <branch>
gh pr create --title "<clear description, under 70 chars>" --body "$(cat <<'EOF'
## Summary
- <what and why, 1-3 bullets>

## Changes
- <key changes>

## Testing
- <Vitest unit + Playwright e2e, security review, code review, QA>

## QA Instructions
<paste QA instructions from step 8a>
EOF
)"
```

**CRITICAL**: No AI attribution anywhere in the PR.

#### e. Merge? Check the `automerge` setting

Read `automerge` from `<project>/.claude/settings.json` (default `false`).

- **`false` (default)** → **stop here.** Report the PR URL and what was built; leave it open for the user to review and merge. Skip step f.
- **`true`** → merge it:

```bash
gh pr merge --squash --delete-branch
```

If merge conflicts:
```bash
git checkout main && git pull origin main
git checkout <branch> && git merge main
```
Resolve conflicts, commit, push, retry merge. Cycle until merged.

#### f. Clean Up (only if merged)

```bash
git checkout main
git pull origin main
git branch -D <branch-name> 2>/dev/null
```

Delete `.claude/branch-progress.md` if it exists.

Report to the user: what was built, tests passing, PR merged.

## Commit Style

Read `commitStyle` from `<project>/.claude/settings.json` (`conventional` if the key/file is absent). The full format for each style lives in `~/.claude/saved-presets/commit-style-<style>.md` (`conventional` or `gitmoji`).
