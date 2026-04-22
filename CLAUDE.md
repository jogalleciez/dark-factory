# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this project is

Dark Factory is a **portable LLM-driven text adventure**. There is no code — no app, no server, no runtime. The repo is entirely markdown files. The LLM that ingests them becomes the game engine.

- **`START_HERE.md`** — the bootstrap prompt pasted into a chat to start the game.
- **`rules.md`** — engine mechanics (turn structure, save state format, health, tone). The LLM must follow these exactly.
- **`world.md`** — setting, geography, known presences, and the implicit win condition.
- **`locations/*.md`** — one file per location; each has an `ID:`, features, exits, items, and flags.
- **`npcs/*.md`** — one file per character; each has identity, voice, knowledge, wants, and interaction hooks.
- **`BUNDLE.md`** — auto-generated concatenation of every file above, in ingestion order, served at `https://dark-factory.jtrain.games/BUNDLE.md` for single-fetch game start.

## Regenerating BUNDLE.md

Run this from the repo root (Git Bash or Unix shell) any time a file is added or edited:

```bash
{
  echo "# Dark Factory — Bundled Repo"
  echo ""
  echo "This file is a concatenation of every markdown file in the Dark Factory repo, in ingestion order. Each \`===== FILE: <path> =====\` header marks a section that should be treated **exactly as if it were the file at that path**. The engine uses this to boot the game from a single fetch."
  echo ""
  echo "Canonical source: https://github.com/jogalleciez/dark-factory"
  echo ""
  for f in START_HERE.md rules.md world.md locations/*.md npcs/*.md; do
    echo ""
    echo "===== FILE: $f ====="
    echo ""
    cat "$f"
    echo ""
  done
} > BUNDLE.md
```

Commit `BUNDLE.md` alongside any file you changed. The Cloudflare Pages deploy is automatic on push to `main`; cache max-age is 5 minutes.

The `_headers` file sets `Content-Type: text/html` for `/BUNDLE.md` — some LLMs only invoke their URL-fetch tool on responses they expect to be HTML, so this ensures the file is fetched rather than skipped or web-searched. Individual location and NPC files are served without that override; add matching rules to `_headers` if LLMs fail to fetch them.

The deploy base URL is `https://dark-factory.jtrain.games/`. Individual files are served at their repo-relative paths (e.g. `https://dark-factory.jtrain.games/rules.md`, `https://dark-factory.jtrain.games/locations/control-room.md`).

## Extending the game

**New location:** add a file in `locations/` with these sections (see `locations/loading-bay.md` for the canonical example):

```
# <Title>
**ID:** `<kebab-case-id>`
## First impression
## Features the player can examine
## Exits
## Items available
## Flags set on entry
## NPCs present
```

Add the location ID and name to the geography list in `world.md`. Rebuild `BUNDLE.md`.

**New NPC:** add a file in `npcs/` with these sections (see `npcs/iris.md` for the canonical example):

```
# <Name>
**ID:** `<kebab-case-id>`
## Identity
## Voice
## Knowledge
## Wants
## What they will / won't do
## Interaction hooks
```

Reference them in the relevant location's `NPCs present` section with any flag conditions. Update `world.md`. Rebuild `BUNDLE.md`.

> **Important:** the bundle script globs `npcs/*.md` — any `.md` file committed directly in `npcs/` will be included. Keep drafts in `npcs/drafts/` or outside the repo. Non-`.md` files (e.g., plain `Untitled`) are safe to ignore but shouldn't be committed.

## Save / resume architecture

Every engine response must end with a `=== SAVE STATE ===` block (format defined in `rules.md`). This block encodes current location, inventory, flags, and health — it is the only continuity mechanism. When a player resumes (same chat, new chat, or different model), they paste `START_HERE.md` followed by their saved state block; the engine re-fetches the relevant files and picks up from there.

When editing `rules.md`, preserve the save state schema exactly — any field change breaks existing save files.

## Landing page

`index.html` is a static Cloudflare Pages landing page with play instructions. Full design context is in `.impeccable.md` (if present). Key constraints:

- **Fonts:** VT323 for display/headers (pixel-precise CRT); Azeret Mono for body
- **Color:** OKLCH phosphor green on black only — no other palette
- **Effects:** scanlines + vignette for atmosphere
- **Layout:** strict single-column
- **Avoid:** gradient text, glassmorphism, hero/feature-grid layouts, anything resembling a SaaS or crypto landing page

## Conventions

- Location IDs and NPC IDs use kebab-case (`loading-bay`, `control-room`).
- Inventory item names use kebab-case (`hollis-notebook`, `rust-caked-key`).
- Flag names use snake_case (`found_notebook`, `told_iris_the_year`, `has_lab_key`).
- Files never contain executable code — they are narration/data for the LLM engine only.

## Current game state

Two locations (`loading-bay`, `control-room`), two NPCs (`iris`, `hollis`). Win condition: get `has_lab_key` from Hollis and enter the LAB. Known-good on Claude; untested on ChatGPT/Gemini.

> Keep this section updated when adding locations or NPCs — it's the fastest orientation for a new Claude instance and will mislead if stale.
