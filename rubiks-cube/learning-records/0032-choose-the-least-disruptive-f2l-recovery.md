# 0032 — F2L: Choose the Least-Disruptive Recovery

**Date:** 2026-08-17  
**Lesson:** `0032-choose-the-least-disruptive-f2l-recovery.html`

## What was taught

- A failed F2L green light should be classified before any recovery turn: positional failure, corner-orientation failure, or edge-readiness failure.
- A named U setup is appropriate only for a viable positional mismatch; U turns cannot change a White-Up corner’s orientation.
- For a corner-orientation or edge-readiness failure, the safe recovery is one normalization trigger through an empty working slot, followed by a complete re-read.
- If another unsolved pair has a full green light, inserting that pair first is less disruptive than recovering an unready pair.

## Key non-obvious insights

- “Least disruptive” is a priority rule, not an algorithm: insert a ready pair, otherwise switch to another ready pair, otherwise normalize once through the protected empty slot.
- Position, orientation, and edge readiness are independent observations, so they require different responses.
- Recovery actions never grant insertion authorization; the two-part green light must be checked again after every setup or normalization.

## Zone of proximal development — next

**F2L: run one complete controlled recovery from a concrete cube state.** Read a failed pair, choose and execute exactly one recovery action, then identify the next valid insertion picture without disrupting completed pairs.
