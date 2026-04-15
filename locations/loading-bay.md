# Loading Bay

**ID:** `loading-bay`

## First impression

A cavernous concrete space, dim except for a single sodium lamp still burning in the far corner. Forklifts sit in rows, their tires cracked flat. The air smells of old grease and, underneath that, something faintly sweet and chemical. A steel catwalk climbs the north wall toward a door stenciled **CONTROL**.

## Features the player can examine

- **Forklifts** — four of them, keys still in the ignition. Batteries long dead. Nothing to take.
- **Sodium lamp** — flickers every few seconds. The fact that it's still lit means something in this building is still feeding it power.
- **Cargo crates** — most are empty. One near the catwalk has been pried open within the last few weeks. Inside: ration wrappers, an empty water pouch, and a notebook with HOLLIS scrawled on the cover in black marker.
- **Hollis's notebook** — can be TAKEn. The early entries are logistics. The later entries get erratic: "I can hear her when I sleep. She says the cultures are beautiful. She says I should see them."
- **Catwalk** — leads UP to the Control Room. The railings are loose but it holds.
- **Loading doors** — the way in. Exiting ends the game.

## Exits

- `UP` / `CLIMB CATWALK` → `control-room`
- `OUT` / `LEAVE` → ends the game (ask the player to confirm first)

## Items available

- `hollis-notebook` — sets flag `found_notebook` when taken.

## Flags set on entry

- `entered_loading_bay`

## NPCs present

None.
