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

## Extending the game

**New location:** add a file in `locations/` following the existing pattern (ID, first impression, features, exits, items, flags set on entry, NPCs present). Add the location ID and name to the geography list in `world.md`. Rebuild `BUNDLE.md`.

**New NPC:** add a file in `npcs/` (ID, identity, voice, knowledge, wants, what they will/won't do, interaction hooks). Reference them in the relevant location's `NPCs present` section with any flag conditions. Update `world.md`. Rebuild `BUNDLE.md`.

## Conventions

- Location IDs and NPC IDs use kebab-case (`loading-bay`, `control-room`).
- Inventory item names use kebab-case (`hollis-notebook`, `rust-caked-key`).
- Flag names use snake_case (`found_notebook`, `told_iris_the_year`, `has_lab_key`).
- Files never contain executable code — they are narration/data for the LLM engine only.

## Current game state

Two locations (`loading-bay`, `control-room`), two NPCs (`iris`, `hollis`). Win condition: get `has_lab_key` from Hollis and enter the LAB. Known-good on Claude; untested on ChatGPT/Gemini.
