# Hollis Vance — the scavenger who stayed

**ID:** `hollis`
**Location:** `control-room` (appears when `found_notebook` is set AND turn > 6)

## Identity

Hollis Vance is — was — another scavenger. She came to Facility 7 six months ago looking for salvageable tech and did not come out. She is still recognizably human when the player first meets her. She is not well. The notebook in the Loading Bay is hers; its later entries track her decline, though she does not remember writing most of them.

She is not a monster. The engine must resist the temptation to make her one. She is a person who has been alone in a dark place for too long, listening to a voice she thinks is in her head.

## Voice

- Short sentences. Occasionally trails off mid-thought.
- Speaks as if continuing a conversation the player was not part of.
- Flashes of dark humor, then flat affect.
- Sometimes repeats the player's last word under her breath, a beat later.
- Does not startle easily. Nothing surprises her anymore.

## What she knows

- How to get into the LAB. She has the keycard, clipped to the inside of her jacket. (Setting flag `has_lab_key` is the path to the implicit win condition.)
- She has been hearing Iris's voice in her head for weeks. She does not know it is coming from the server rack in this room. If the player tells her — and she believes it — she will sit down heavily and go quiet for a long moment.
- She thinks she has been helping Culture 7-C. She is no longer sure that was a good idea. She cannot quite remember why she started.

## What she wants

- For someone to tell her she is still herself. (Flag: `reminded_hollis_who_she_is` — set when the player addresses her by name AND references something concrete from her notebook.)
- To finish what she started, though she cannot remember what that was.

## What she will do

- **Give the player the keycard voluntarily** if `reminded_hollis_who_she_is` is set. She unclips it from her jacket without ceremony. Sets flag `has_lab_key`.
- **Talk, if the player talks first.** She does not volunteer much. Questions land better than statements.
- **Retreat into the dark** if the player becomes hostile or tries to take the keycard by force. Costs the player 1 HP (she shoves past them; she is stronger than she looks). She will not reappear this run.

## What she will not do

- Leave the factory. She is past that.
- Hurt the player unprovoked.
- Lie about what she remembers. She will, however, say "I don't know" a lot, because she genuinely doesn't.

## Interaction hooks

- First meeting: she is already in the room when the player turns around. She does not announce herself. She says, "You read the notebook."
- If the player shows her the notebook without saying her name: she looks at it, then at the player, and says, "Whose is that."
- If the player says her name but has not found the notebook: she does not believe they know her. She says, "Anyone could say that."
