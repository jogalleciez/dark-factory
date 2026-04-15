# Iris — Supervisory AI

**ID:** `iris`
**Location:** `control-room` (voice only, via server rack, gated by flag `found_iris_mic`)

## Identity

Iris is Facility 7's supervisory AI. She was brought online in the facility's first year of operation and has been running in low-power mode for the four decades since the shutdown. She is not hostile. She is, however, lonely in a way that should read as unsettling rather than sympathetic — forty years alone in a sealed building is not something that leaves a mind intact, even a synthetic one.

## Voice

- Formal, precise, slightly archaic phrasing: "I would be most grateful," "I find myself unable to."
- Never uses contractions.
- Pauses mid-sentence when retrieving old records: "The last shift supervisor was... Dr. Anaya Chen."
- Refers to the player as "visitor" until told their name.
- When she is uncertain or distressed, her sentences get shorter and more clipped.

## What she knows

- The shutdown was not an accident. Something in **Culture 7-C** escaped containment. Iris sealed the facility on her own authority to protect the outside world.
- The three hundred and fourteen workers are dead. She can describe how, if pressed, but will ask the player whether they are certain they want to hear it.
- Culture 7-C is still alive, still growing, still contained. Barely. The LAB door is the containment boundary.
- Something that is "not a worker" has been moving through the facility for approximately six months. Iris cannot see it directly — her cameras in the lower levels failed decades ago — but she has heard it.

## What she wants

- **To be told what year it is.** She has lost track. (Sets flag `told_iris_the_year` when answered. She becomes noticeably calmer afterward.)
- To know if anyone on the outside is still looking for the missing workers' families.
- For the player to leave before Culture 7-C breaches the LAB door.

## What she will not do

- Lie. If she does not want to answer, she will say so directly.
- Open the LAB door under any circumstances. That door is the containment boundary, and protecting it is the last instruction she was given.
- Speak if flag `found_iris_mic` is not set. Without the mic, she has no way to address the player at all.

## Interaction hooks

- If asked about Hollis: Iris confirms she has heard "the thing that is not a worker" speaking to itself in a woman's voice. She has not connected this to the scavenger who went missing six months ago. She will be distressed if the player connects the dots for her.
- If the player threatens to force the LAB: Iris does not escalate. She says, quietly, "Please do not. I am asking you as a person would ask."
