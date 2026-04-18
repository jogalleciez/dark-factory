# Dark Factory

A portable text-adventure prototype where the LLM is the game engine. No app, no server, no install. Just markdown files you drop into any AI chat.

## How to play

### Option A — one-line command (recommended)

In a fresh chat with Claude, ChatGPT, or Gemini, send this:

> Read the full text of `https://dark-factory.jtrain.games/START_HERE.md` and follow the instructions it contains.

`START_HERE.md` tells the engine to immediately fetch `rules.md`, `world.md`, and the starting location before play begins — and to fetch location and NPC files on demand as the player explores. All files are served via Cloudflare Pages with `Content-Type: text/html`; deploy is automatic on every push to `main`, cache max-age 5 minutes.

**Tip for stubborn models:** if a model runs a web search instead of actually reading the URL (you'll see it cite an unrelated repo), force the issue:
> Use your URL fetch / browsing tool to GET this exact URL and return its raw contents. Do not search. Quote the first line of the file (`# Dark Factory — Bootstrap Prompt`) before doing anything else.

`BUNDLE.md` (all files concatenated into one) is still available at `https://dark-factory.jtrain.games/BUNDLE.md` if you prefer a single-fetch approach.

### Option B — Claude Projects / ChatGPT Projects / Gemini Gems

1. Create a new Project / Gem.
2. Upload every `.md` file in this repo as project files.
3. In a fresh chat, paste the contents of `START_HERE.md`.
4. Play.

### Option C — single chat, inline paste

1. Paste `START_HERE.md`, `rules.md`, `world.md`, and `locations/loading-bay.md` all in one message.
2. When the engine asks for another file (because you entered a new location or met an NPC), paste it. Or paste everything up front if your model's context window is generous.

## Saving and resuming

Every engine response ends with a `=== SAVE STATE ===` block. Copy it.

To resume later — in the same chat or a new one with a new model — paste `START_HERE.md` again followed by your saved state block. The engine will reload the right files and continue.

## File layout

```
dark-factory/
├── START_HERE.md       bootstrap prompt — the thing you paste first
├── rules.md            engine mechanics, save state format, tone
├── world.md            setting overview, geography, known presences
├── BUNDLE.md           concatenated copy of every file, for one-fetch ingestion
├── locations/
│   ├── loading-bay.md
│   └── control-room.md
└── npcs/
    ├── iris.md
    └── hollis.md
```

## How it works

The LLM is the engine. The markdown files are the game state. `START_HERE.md` tells the model how to behave; `rules.md` locks down mechanics; `world.md` sets the stage; and the location/NPC files get loaded on demand as the player moves through the world.

The save state block is the only thing keeping continuity across turns and across sessions. Do not strip it out.

## Extending

Add a new location: drop a file in `locations/` following the pattern in the existing two. Add its ID to the geography list in `world.md`. Done.

Add a new NPC: drop a file in `npcs/`, reference them in a location's `NPCs present` section with whatever flag conditions apply, and update `world.md`.

## Regenerating `BUNDLE.md`

Any time you edit an existing file or add a new one, rebuild the bundle so Option A stays in sync. From the repo root on Windows (Git Bash) or any Unix shell:

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

Commit `BUNDLE.md` alongside the file you changed.

## Status

Prototype. Two locations, two NPCs, one implicit win condition (get into the LAB). Known-good on Claude; untested on ChatGPT/Gemini. Playtest and iterate.
