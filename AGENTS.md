# Dark Factory — Agent Guide

## Project Overview

Dark Factory is a **portable LLM-driven text adventure** with two deployment targets:

1. **Pure markdown / chat-native** — Players paste `START_HERE.md` into any AI chat (Claude, ChatGPT, Gemini, etc.). The LLM reads remote markdown files over HTTPS and acts as the game engine. No app, no server, no install.
2. **Reddit Devvit app** — An interactive post inside Reddit that wraps the same adventure in a React client + Hono server, using Gemini 2.5 Flash as the engine and Redis for per-player save state.

The repo root holds the markdown game source and a static landing page. The `devvit/` subdirectory is a self-contained TypeScript app for the Reddit wrapper.

---

## Repository Layout

```
dark-factory/
├── START_HERE.md          # Bootstrap prompt — the first thing a player pastes
├── rules.md               # Engine mechanics, save-state schema, tone constraints
├── world.md               # Setting overview, geography, known presences
├── BUNDLE.md              # Auto-generated concatenation of all markdown files
├── index.html             # Static landing page (Cloudflare Pages)
├── _headers               # Cloudflare Pages header overrides (Content-Type, cache)
├── locations/
│   ├── loading-bay.md            # Starting location
│   ├── manufacturing-facility.md # Assembly floor (between bay and control room)
│   └── control-room.md           # Win-condition room (Iris, Hollis, LAB)
├── npcs/
│   ├── iris.md            # Supervisory AI NPC
│   └── hollis.md          # Scavenger NPC
└── devvit/                # Reddit Devvit app (separate package)
    ├── package.json
    ├── devvit.json        # Devvit app manifest
    ├── tsconfig.json
    ├── vite.config.ts
    ├── build-server.mjs   # esbuild script for server bundle
    └── src/
        ├── shared/types.ts
        ├── client/
        │   ├── index.html
        │   ├── main.tsx
        │   ├── App.tsx
        │   └── components/
        │       ├── Terminal.tsx
        │       └── CommandInput.tsx
        └── server/
            ├── index.ts          # Hono routes, rate limiting, menu actions
            ├── llm.ts            # Gemini API client, prompt builder, save-state parser
            ├── save-state.ts     # Redis read/write for game state
            ├── game-content.ts   # Embedded markdown strings (rules, world, locations, NPCs)
            └── devvit-context.ts # Typed context helper for Devvit runtime
```

---

## Technology Stack

### Root (static site + game data)
- **Host:** Cloudflare Pages
- **Landing page:** Single-file HTML/CSS (no build step)
- **Assets:** Markdown files served as static content
- **Deploy:** Automatic on push to `main`

### Devvit app (`devvit/`)
- **Runtime:** Reddit Devvit platform (Node.js-like sandbox)
- **Client:** React 18 + Vite
- **Server:** Hono 4.x + `@hono/node-server` adapter
- **Bundler:** Vite (client), esbuild (server)
- **Language:** TypeScript 5.6 (ES modules, `"type": "module"`)
- **External API:** Google Gemini 2.5 Flash (`generativelanguage.googleapis.com`)
- **Storage:** Devvit Redis (per-user, per-post save state)
- **Dev CLI:** `devvit` (playtest, deploy, settings)

---

## Build and Run Commands

### Root (landing page + markdown)
No build step. Files are served statically by Cloudflare Pages.

Regenerate `BUNDLE.md` after any edit to a markdown file (Git Bash or Unix shell):

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

### Devvit app
All commands run from the `devvit/` directory:

```bash
npm install
npm run typecheck      # tsc --noEmit
npm run build          # builds client (Vite) + server (esbuild)
npm run build:client   # vite build only
npm run build:server   # node build-server.mjs only
npm run dev            # devvit playtest (deploys to r/dark_factory_dev)
```

Setting the Gemini API key (required before playtest):

```bash
npx devvit settings set geminiApiKey
```

Verify:
```bash
npx devvit settings list
```

---

## Code Organization

### Game content flow
1. **Markdown files** (`locations/*.md`, `npcs/*.md`) are the canonical design source.
2. For the Devvit server, `game-content.ts` embeds the *same* text as string constants (`RULES_MD`, `WORLD_MD`, `LOADING_BAY_MD`, etc.).
3. `llm.ts` assembles a system prompt from rules + world + current location + active NPCs + current save state.
4. The prompt is sent to Gemini; the response is parsed for narrative text and a `=== SAVE STATE ===` block.
5. `save-state.ts` persists the parsed state to Redis under `game:{userId}:{postId}`.

### Client-server API
- `GET /api/state` — returns current save state (or `null` if no game).
- `POST /api/newgame` — clears state, asks Gemini for an opening narration, persists initial state.
- `POST /api/command` — sends player command to Gemini, parses response, persists new state.

### Rate limiting & safety
- Commands capped at **200 characters**; C0 control chars stripped.
- **20 commands per user per 60 seconds**, enforced via Redis (`rate:{userId}`).
- Errors mapped to user-friendly strings (`MissingKeyError`, `UpstreamError`, `TimeoutError`).

---

## Code Style Guidelines

- **TypeScript:** Strict mode enabled. ES2020 target, bundler module resolution.
- **Imports:** Use `.js` extensions for relative imports (e.g., `./llm.js`), even for `.ts` source files. This is required by the `"moduleResolution": "bundler"` + isolated modules setup.
- **Formatting:** No explicit formatter configured. Follow existing style: 2-space indent, single quotes, semicolons.
- **Client styles:** Inline `style` objects (no CSS-in-JS library). Colors use OKLCH phosphor green (`oklch(... 140)`). Fonts: `VT323` for display, `Azeret Mono` for body.
- **Naming:**
  - Location IDs: `kebab-case`
  - Inventory items: `kebab-case`
  - Flags: `snake_case`

---

## Testing

There is **no automated test suite** in this repository. Validation is manual:

1. **Type check:** `npm run typecheck` (devvit/)
2. **Build:** `npm run build` must complete without errors.
3. **Playtest:** `npm run dev` deploys to `r/dark_factory_dev`. Test new locations/NPCs via the Reddit post.
4. **Markdown purity:** Ensure no executable code leaks into `.md` files — they are narration/data for the LLM engine only.

---

## Deployment

### Cloudflare Pages (root)
- Pushes to `main` auto-deploy.
- `_headers` forces `Content-Type: text/html; charset=utf-8` and `Cache-Control: public, max-age=300` for all `.md` files and `BUNDLE.md`. This is deliberate: some LLMs only invoke URL-fetch tools on HTML responses.
- Base URL: `https://dark-factory.jtrain.games/`

### Reddit Devvit
- Playtest target subreddit: `r/dark_factory_dev` (configured in `devvit.json`).
- The app requires a global secret setting `geminiApiKey`.
- Menu action (`/internal/menu/create-post`) lets moderators create a new interactive post from the subreddit sidebar.

---

## Security & Privacy Considerations

- **Gemini free tier trains on submitted data.** The Devvit README explicitly warns about this — a paid Gemini key is strongly preferred for any public release.
- **No PII is sent to Gemini.** Outbound content is only the embedded game markdown + save state + player command. The Reddit `userId` never leaves the Devvit runtime; it is used only as a Redis key scope.
- **Save state is per-(user, post).** Players cannot read each other's saves through the app.
- **API key redaction:** `geminiApiKey` is declared `isSecret: true` in `devvit.json`. It is sent as the `x-goog-api-key` header, not as a query parameter, so it does not appear in Gemini error response bodies.
- **Required for public release:** Devvit requires Terms of Service and Privacy Policy URLs. Draft pages on the Cloudflare Pages site (e.g. `/privacy.html`) and link them in the Devvit dashboard before switching from playtest to public.

---

## Extending the Game

### Add a new location
1. Create `locations/<id>.md` with sections: `First impression`, `Features`, `Exits`, `Items available`, `Flags set on entry`, `NPCs present`.
2. Add the location to the geography list in `world.md`.
3. Add the embedded string + switch case in `devvit/src/server/game-content.ts`.
4. Rebuild `BUNDLE.md`.

### Add a new NPC
1. Create `npcs/<id>.md` with sections: `Identity`, `Voice`, `What they know`, `What they want`, `What they will / won't do`, `Interaction hooks`.
2. Reference them in the relevant location's `NPCs present` section with flag conditions.
3. Update `world.md`.
4. Add the embedded string + switch case in `devvit/src/server/game-content.ts`.
5. Update `activeNpcs()` logic in `devvit/src/server/llm.ts` if gating conditions are new.
6. Rebuild `BUNDLE.md`.

### Edit design docs
- `.impeccable.md` contains the landing-page design brief (phosphor terminal aesthetic).
- `CLAUDE.md` contains project conventions and the current game-state summary. Keep it updated when adding content.

---

## Key Files for Quick Reference

| File | Purpose |
|------|---------|
| `START_HERE.md` | Bootstrap prompt for LLM chat play |
| `rules.md` | Engine rules and save-state schema |
| `world.md` | Canon geography and win condition |
| `_headers` | Cloudflare Pages content-type overrides |
| `devvit/devvit.json` | App manifest, permissions, settings schema |
| `devvit/src/server/llm.ts` | Gemini integration, prompt assembly, response parsing |
| `devvit/src/server/game-content.ts` | Embedded markdown copies for server bundle |
| `devvit/src/server/save-state.ts` | Redis persistence layer |
| `devvit/src/server/index.ts` | Hono routes, rate limiting, error mapping |
