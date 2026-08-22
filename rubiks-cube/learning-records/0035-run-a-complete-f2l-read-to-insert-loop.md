# 0035 — Run a Complete F2L Read-to-Insert Loop

**Date:** 2026-08-22  
**Lesson:** `0035-run-a-complete-f2l-read-to-insert-loop.html`

## What was taught

- A complete basic F2L action loop is: identify pair and centre-defined empty slot → select the working side from the corner’s white-sticker direction → name one viable `0`, `U`, `U2`, or `U′` setup → verify the two-part green light → insert once.
- The right-side destination is `UFR + UF` with white facing Right; the left-side destination is `UFL + UF` with white facing Left. In both cases, the matching edge at `UF` must have its front sticker matching the front centre.
- A named setup creates only a new observation point. It never authorizes `R U R′` or `L′ U′ L` automatically.
- When authorization fails, the solver must stop: insert another fully ready pair if available, otherwise use one normalization through an empty working slot and take a fresh read.

## Key non-obvious insights

- The trigger is the final, conditional action—not an exploratory tool after a promising setup.
- `0` is a valid planned setup: it explicitly confirms that the pair already occupies destination positions, while still requiring both green-light checks.
- A successful loop can end in “not authorized” and recovery; disciplined stopping keeps solved F2L pairs protected and the state legible.

## Zone of proximal development — next

**F2L: execute and audit one complete loop on a physical scramble.** Use a compact observation log to distinguish a planning error (wrong pair, side, or named setup) from a green-light failure or execution error, then correct only that stage on the next attempt.
