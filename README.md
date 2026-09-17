# mm-claude-starter

Starter kit for Claude Code — skills, hooks, output styles, and config presets. Clone or fork to get a working `.claude/` setup with git workflow skills, permission presets, commit style presets, and fun output styles.

## Setup

Copy `.claude/` and `CLAUDE.md` into your project root, or clone this repo and symlink what you need.

## What's Included

- **Skills** — `/b`, `/w`, `/cpr`, `/cprmc`, `/a`, `/c`, `/ping`, and more (run `/skills` to list all)
- **Saved presets** — permissions (loose/tight) and commit styles (conventional/gitmoji) in `.claude/saved-presets/`
- **Output styles** — caveman and grug-speak in `.claude/output-styles/`
- **CLAUDE.md** — quality standards, stack reference, project list

## Recommended Add-Ons

- [cship.dev](https://cship.dev) — status bar for Claude Code: `curl -fsSL https://cship.dev/install.sh | bash`
- [peonping.com](https://peonping.com) — terminal audio notifications when Claude is done

## Shared CI Workflows

Two reusable workflows live in `.github/workflows/` so every repo calls one instead of
maintaining its own copy. Change the verify loop here and it lands everywhere on the next run.

**Node** — install → lint → typecheck → test → build. An empty script name skips that gate, so a
repo without tests says so rather than faking a green suite.

```yaml
jobs:
  verify:
    uses: mark-mcdermott/mm-claude-starter/.github/workflows/node-verify.yml@main
    with:
      node-version: "25"
      lint-script: lint  # off by default; not every repo has one
      test-script: ""    # no suite yet
```

**Bash** — shellcheck + a test command, optionally across a runner matrix.

```yaml
jobs:
  verify:
    uses: mark-mcdermott/mm-claude-starter/.github/workflows/bash-verify.yml@main
    with:
      shellcheck-paths: puravida
      test-command: bats test/
      install-bats: true
      os: '["ubuntu-latest", "macos-latest"]'
```

Callers track `@main` on purpose: a fix here propagates without touching each repo. The
tradeoff is that a bad change here breaks every repo at once, so the daily scheduled runs
are what catch it.

### Dependabot

Each repo needs its own `.github/dependabot.yml` — there is no `uses:` for it. Copy this,
dropping the `npm` block for repos with no package manifest. The `groups` key is the part
that matters: ungrouped, a first run opens a dozen PRs and you stop reading them.

```yaml
version: 2
updates:
  - package-ecosystem: npm
    directory: /
    schedule: { interval: weekly, day: monday }
    open-pull-requests-limit: 5
    groups:
      dev-dependencies:
        dependency-type: development
        update-types: [minor, patch]
      production-patch:
        dependency-type: production
        update-types: [patch]
    # Majors stay ungrouped — they need reading, not batching.

  - package-ecosystem: github-actions
    directory: /
    schedule: { interval: weekly, day: monday }
    groups:
      actions:
        patterns: ["*"]
```
