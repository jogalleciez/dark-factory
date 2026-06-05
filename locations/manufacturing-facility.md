# Manufacturing Facility

**ID:** `manufacturing-facility`

## First impression

The catwalk opens onto a vast assembly floor. A conveyor line runs the length of the room, belts still creeping forward in fits and starts, feeding nothing. Overhead, articulated robotic arms hang frozen mid-gesture, then twitch — servos waking, swinging, settling — on a cycle no one scheduled. Half-finished trays of sealed vials sit abandoned along the line, their condensation long since dried to white rings.

## Features the player can examine

- **Conveyor line** — still powered, lurching forward a few feet at a time, feeding nothing. Reaching across it or stepping onto the belt while it moves is how people lose fingers. *(Hazard — see below.)*
- **Robotic arms** — six of them on an overhead gantry, swinging on an irregular cycle. They are not aiming at the player. They are still trying to do their job. *(Hazard — see below.)*
- **Assembly trays** — rows of sealed vials, most empty. One tray near the line still holds a single intact vial, fogged with cold. Can be TAKEn, but only once the line has stopped.
- **Emergency-stop lever** — a red mushroom-head switch on a pillar, grimed but unbroken. PULLing it halts the conveyor and locks the arms in place. (Sets flag `stopped_conveyor`.)
- **Far stairs** — a short steel stair at the back of the floor climbs to a door marked CONTROL.

## Hazard

- While the conveyor is running (flag `stopped_conveyor` NOT set), any action that crosses the line — reaching for the vial, stepping onto the belt, squeezing between the arms — costs 1 HP and does not succeed. Narrate the machinery catching the player; it is not malice, just a machine doing its job.
- PULLing the emergency-stop lever sets `stopped_conveyor` and removes this hazard for the rest of the game. After that the floor is safe and the vial can be taken.

## Exits

- `UP` / `STAIRS` / `CONTROL` → `control-room`
- `DOWN` / `CATWALK` → `loading-bay`

## Items available

- `culture-sample` — the single intact vial. Only takeable after `stopped_conveyor` is set. Sets flag `found_culture_sample` when taken.

## Flags set on entry

- `entered_manufacturing_facility`

## NPCs present

None.
