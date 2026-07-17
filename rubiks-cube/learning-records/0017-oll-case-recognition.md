# 0017 — OLL Case Recognition

**Date:** 2026-07-17  
**Lesson:** `0017-oll-case-recognition.html`

## What was taught

- A compact 2-Look OLL recognition decision tree: read **yellow edges first**, then yellow corners.
- The four edge patterns and setups: dot (apply once, then re-read), L (yellow edges at back + left), line (horizontal), and cross (move to corner recognition).
- After a yellow cross: four yellow-up corners means OLL is complete; one corner at back-left uses Sune; one at back-right uses Anti-Sune; zero corners uses one Sune from any angle, then re-read.
- A whole-cube `y` rotation is the safe setup move during OLL; a `U` turn changes the last-layer piece arrangement and should not be used to position the pattern.

## Key non-obvious insights

- Recognition is a strict two-level decision: the corner pattern is irrelevant until the yellow cross exists.
- “No yellow corners” is a normal OLL state, not a failure; it intentionally converts to a recognizable one-corner state.
- Saying the case aloud before turning creates a check between visual recognition and algorithm execution, reducing trial-and-error during full solves.

## Zone of proximal development — next

- **F2L pair recognition drills:** find a corner’s matching edge and its target slot immediately, then practise planning the next pair while inserting the current one.
- Continue full solves with the OLL caller: name the edge case, then the corner case, before every OLL algorithm until the decision tree is automatic.
