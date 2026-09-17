---
name: wrap
description: /wrap — commit, PR, merge, cleanup (full branch wrap-up)
usage: /wrap
allowed-tools:
  - Bash(git:*)
  - Bash(gh:*)
  - Read
---

# Commit, PR, Merge & Cleanup

Wrap up a feature branch in one shot: commit all changes, create a PR, merge it, then clean up.

## Workflow

### 1. Pre-Commit Checks
Run the project's typecheck, lint, and test commands (check package.json or project config for available scripts).
Fix any issues before proceeding.

### 2. Check Status & Stage
```bash
git branch --show-current
git status
git diff --stat
git add [files]
```
- If on `main`, stop — this skill is for feature branches only.
- Save the branch name for cleanup later.

### 3. Commit
Read `commitStyle` from `<project>/.claude/settings.json` (`conventional` if absent) and write the commit message following that style exactly — its format lives in `~/.claude/saved-presets/commit-style-<style>.md`.

**Commit rules (CRITICAL)**:
- Follow the format and rules in the commit style file.
- No AI attribution. No co-author lines, no signatures, no references to Claude/AI.
- Commit as the developer, never as Claude.

### 4. Analyze Full Branch
Review **all commits** on the branch (not just this one) for the PR summary.
```bash
git log --oneline main..HEAD
git diff main...HEAD --stat
```

### 5. Push & Create PR

Open it **ready for review** — never `--draft`.
```bash
git push -u origin [branch]
gh pr create --title "Brief description" --body "$(cat <<'EOF'
## Summary
- What was done and why (1-3 bullets)

## Changes
- Key change 1
- Key change 2

## Testing
- How it was tested (typecheck, lint, e2e, manual)
EOF
)"
```

**PR rules**:
- Title under 70 characters, no AI attribution
- No commit type prefix in PR title — just a clear description

### 6. Merge the PR
```bash
gh pr merge --squash --delete-branch
```
Use squash merge to keep main history clean.

### 7. Cleanup
```bash
git checkout main
git pull origin main
```
- Confirm the branch is deleted locally and on remote.
- Show `git branch` to verify.

### 8. Report
- Show the merged PR URL
- Confirm cleanup is complete
