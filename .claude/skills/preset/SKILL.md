---
name: preset
description: /preset [<axis> <value>] — show or set THIS project's config in <project>/.claude/settings.json (commit · permissions · automerge · stack)
usage: /preset [commit conventional|gitmoji | permissions loose|tight | automerge on|off | stack <ACRONYM> | reset]
examples:
  - /preset
  - /preset commit gitmoji
  - /preset automerge on
  - /preset permissions tight
  - /preset stack DNC-BARBAWSZ
  - /preset reset
allowed-tools:
  - Bash(git:*)
  - Read
  - Write
  - Edit
  - Glob
---

# Per-Project Preset Editor

Shows or changes the current project's config in `<project>/.claude/settings.json`. Edits **only** the project you're in — **never** global `~/.claude`. To establish these when starting a project, use `/new`.

## Keys
`permissions` (loose|tight) · `commitStyle` (conventional|gitmoji) · `automerge` (on|off) · `stack` (any name in `~/Dev/_PROJECTS/README.md` → Stacks)

## Behavior

### `/preset` (no args) — show
Read `<project>/.claude/settings.json` and print current values:
- `commitStyle`, `automerge`, `stack` — straight from their keys.
- permissions — detect `loose` vs `tight` by comparing the `permissions` object to `~/.claude/saved-presets/permissions-*.json` (else report `custom`).

If the file doesn't exist, say so, show the global defaults (`~/.claude/CLAUDE.md` → Defaults), and suggest `/new` for a fresh project.

### `/preset <axis> <value>` — set one key
Locate (or create, seeded from global defaults) `<project>/.claude/settings.json`, then:

| axis | values | action |
|---|---|---|
| `commit` | conventional · gitmoji | set `commitStyle` |
| `permissions` | loose · tight | replace `permissions` with the object from `~/.claude/saved-presets/permissions-<value>.json` |
| `automerge` | on · off | set `automerge` (true/false) |
| `stack` | any stack name defined in `~/Dev/_PROJECTS/README.md` → Stacks (validate against that file, never a cached list) | set `stack` |

- Reject an unknown axis or value with a one-line usage hint — change nothing.
- Preserve all other keys and the file's formatting.
- Confirm the change as `old → new`.

### `/preset reset`
Restore `commitStyle`, `permissions`, and `automerge` to the global defaults (conventional · loose · off). Leave `stack` as-is (it's project-specific, not a default). Confirm what changed.

## Notes
- `permissions` is enforced by the harness; `commitStyle` / `automerge` / `stack` are read by the build skills at action time.
- Never touches `~/.claude/`. Global defaults are changed by editing `~/.claude/CLAUDE.md` / `~/.claude/settings.json` directly.
