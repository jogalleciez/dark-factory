# Control Room

**ID:** `control-room`

## First impression

A long room lined with monitors — most dark, three still glowing a dim amber. The air is warm in a way the rest of the building is not. A server rack in the back corner hums steadily, fans spinning after forty years with no one to clean them. A chair is pulled up to the central console. The cushion is still warm.

## Features the player can examine

- **Amber monitors** — slow scroll of sensor readings: temperature, pressure, a row labeled `CULTURE 7-C: STABLE`. The timestamp in the corner updates in real time.
- **Central console** — a keyboard with letters worn off the most-used keys. A coffee mug sits beside it. The coffee inside is cold but has not yet grown anything.
- **Chair** — pulled out at an angle, as if someone stood up in a hurry. The cushion holds body heat.
- **Server rack** — warm to the touch. A small green indicator blinks in sync with the fans. If the player examines it closely or puts their ear near it: there is a microphone built into the top panel. (Sets flag `found_iris_mic`. Without this flag, Iris cannot speak to the player.)
- **Door marked LAB** — reinforced, locked. A keycard slot blinks red. Cannot be forced. (Requires flag `has_lab_key`.)

## Exits

- `DOWN` / `CATWALK` → `loading-bay`
- `LAB` → requires flag `has_lab_key`; otherwise the door stays red.

## Flags set on entry

- `entered_control_room`

## NPCs present

- **Iris** — voice only, through the server rack, but **only after** flag `found_iris_mic` is set.
- **Hollis** — appears in the Control Room if flag `found_notebook` is set AND turn counter > 6. She has been watching from a maintenance alcove behind the server rack. Load `npcs/hollis.md` when she appears.

## Notes to the engine

- The warmth, the coffee, and the warm chair all imply someone was here *minutes* ago. That someone is Hollis. Do not spell it out; let the player work it out.
- If the player tries to force the LAB door, narrate the attempt failing and cost 0 HP. The door is not a hazard, just a wall.
