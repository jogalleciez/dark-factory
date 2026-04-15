# Dark Factory

A portable text-adventure prototype where the LLM is the game engine. No app, no server, no install. Just markdown files you drop into any AI chat.

## How to play

### Option A — one-line command (recommended)

In a fresh chat with Claude, ChatGPT, or Gemini, send this:

> Fetch `https://uithub.com/jogalleciez/dark-factory` and follow the instructions in `START_HERE.md`.

[uithub.com](https://uithub.com) flattens the whole repo into a single text blob the model can ingest in one fetch. Every location and NPC file arrives in context together, so the engine has the full world available as it plays. [gitingest.com/jogalleciez/dark-factory](https://gitingest.com/jogalleciez/dark-factory) works the same way if uithub is down.

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

## Status

Prototype. Two locations, two NPCs, one implicit win condition (get into the LAB). Known-good on Claude; untested on ChatGPT/Gemini. Playtest and iterate.
