---
name: new
description: /new <name> <stack> — bootstrap a project: scaffold the stack and stamp <project>/.claude/settings.json (permissions, commit style, automerge, stack)
usage: /new <name> <STACK> [commit=conventional|gitmoji] [perms=loose|tight] [automerge=on|off]
examples:
  - /new loopixel RW
  - /new retireat55 DNC-BARBAWSZ automerge=on
  - /new no-dinos RAW perms=tight commit=gitmoji
allowed-tools:
  - Bash(npm:*)
  - Bash(npx:*)
  - Bash(node:*)
  - Bash(git:*)
  - Bash(mkdir:*)
  - Bash(ls:*)
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Bootstrap a New Project

Scaffolds a new project in the right stack and stamps its per-project config into `<project>/.claude/settings.json`. This is the "establish it once when I start" moment for **permissions, commit style, automerge, and stack**.

## Arguments

- **name** (required): project slug, e.g. `loopixel`.
- **stack** (required): any name in `~/Dev/_PROJECTS/README.md` → Stacks. **Read that file — never a list cached here**; the names change. If missing or unknown, print the current set with their one-line expansions and ask — the one exception to running autonomously.
- Optional overrides (else use the global defaults in `~/.claude/CLAUDE.md` → Defaults):
  - `commit=conventional|gitmoji` (default `conventional`)
  - `perms=loose|tight` (default `loose`)
  - `automerge=on|off` (default `off`)

## Steps

### 1. Resolve & validate
- Confirm `stack` appears in `~/Dev/_PROJECTS/README.md` → Stacks, and read its expansion from there. The expansion drives everything in step 2.
- Location (follow the convention): `~/Dev/<name>-proj/<name>`. **Stop if it already exists.**

### 2. Scaffold the base framework

Pick the base from the shell the expansion names:

| Expansion contains | Base scaffold |
|---|---|
| **Astro** (the `DNC-BARBAW*` family, `RAW`, `AWZR`, `AW`) | `npm create astro@latest <name> -- --template minimal --typescript strict` |
| **neXt** (`DUCXZ-WSRRANT`) | `npx create-next-app@latest <name> --ts --tailwind --eslint --app --src-dir --use-npm` |
| neither (`RW`, `CRT`) | `npm create vite@latest <name> -- --template react-ts` |

Then install **per letter, not per acronym** — a new name built from known letters scaffolds
without editing this skill:

| Letter | Install |
|---|---|
| **W** tailWind | `tailwindcss` + `@tailwindcss/vite`; `@import "tailwindcss"` in the global stylesheet |
| **R** React *(in an Astro name)* | `@astrojs/react` + `react` + `react-dom` — islands, never whole pages |
| **S** Shadcn | `npx shadcn@latest init` — **style `base-nova`** (Base UI). `new-york` (Radix) only for `DUCXZ-WSRRANT` |
| **D** + **N** | `drizzle-orm`, `drizzle-kit`, `@neondatabase/serverless`; schemas in `src/db/schema/` |
| **Z** Zod | `zod` |
| **BA** Better Auth | `better-auth` + its Drizzle adapter |
| **B** Blob / **U** Uploadthing | `@vercel/blob` / `uploadthing` + `@uploadthing/react` |
| **Q** Query | `@tanstack/react-query` |
| **C** Capacitor | `@capacitor/core`, `@capacitor/cli` |
| **T** Tauri | `@tauri-apps/cli` |
| **R** Resend *(in `AWZR`)* | `resend` |
| **C** CodeMirror *(in `CRT`)* | `@codemirror/state`, `@codemirror/view`, `@codemirror/commands` |

**All stacks:** `@vercel/analytics`.

Three letters are ambiguous — resolve from the expansion in the README, never the letter
alone: `R` is React, Radix or Resend depending on the name; `C` is Capacitor except in `CRT`,
where it is CodeMirror; `A` is Astro except inside `BA`, and in `DUCXZ-WSRRANT` where it means
hand-rolled auth. **Never scaffold hand-rolled auth into a new project** — that letter exists
to describe diamondheart as it is, not as a template.

Deep stack wiring (auth flows, Capacitor/Tauri native shells, Neon provisioning, Stripe) is **guided, not faked** — set up what installs cleanly headlessly, and record anything that needs accounts or interactive steps as a `## Setup TODO` in the project CLAUDE.md (step 4). Never report a piece as done if it isn't.

### 3. Stamp `<project>/.claude/settings.json`

Create `.claude/` and write:
```json
{
  "permissions": <the "permissions" object from ~/.claude/saved-presets/permissions-<loose|tight>.json>,
  "commitStyle": "<conventional|gitmoji>",
  "automerge": <true|false>,
  "stack": "<STACK>"
}
```
- `permissions`: copy the `permissions` object verbatim from the matching `~/.claude/saved-presets/permissions-*.json`. The harness enforces it natively.
- `commitStyle`, `automerge`, `stack`: read by the build skills (`/branch`, `/preset`) at action time.

### 4. Project CLAUDE.md
Create `<project>/CLAUDE.md` with:
- `**Stack:** <STACK>` + the one-line expansion.
- A one-line config note: `Commit: <style> · Automerge: <on/off>` (human-visible mirror).
- A `## Setup TODO` list for any stack pieces left unfinished in step 2.

### 5. First commit
- `git init`, stage everything, commit following the chosen commit style (read `~/.claude/saved-presets/commit-style-<style>.md` for the format).
- **Zero AI/Claude attribution** anywhere — commit as the developer.

### 6. Report
Name · location · stack · the four config values · any Setup TODOs left for the user.

## Notes
- Source of truth for these four settings is `<project>/.claude/settings.json`. Global fallback defaults live in `~/.claude/` (CLAUDE.md → Defaults).
- To change one later, run `/preset <axis> <value>` inside the project.
