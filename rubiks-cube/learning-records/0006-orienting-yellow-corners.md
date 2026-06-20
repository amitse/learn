# 0006 — Orienting Yellow Corners

**Date:** 2026-06-20
**Lesson:** `0006-orienting-yellow-corners.html`

## What was taught

- After the Yellow Cross exists (lesson 5), yellow corners may not face up
- **OLL Step 2**: orient all 4 top-layer corners yellow-up using a single algorithm
- No case recognition needed beyond: "is yellow at BR?"

## The Sune Algorithm

**R U R′ U R U2 R′**

The defining feature is the **U2** (double U turn) before the final R′ — distinguishes it from
the Right Trigger variants. Memory hook: "almost double Right Trigger, then continue with R U2 R′."

## The 3-Step Rule

| # | Action | Tool |
|---|---|---|
| 1 | Find any corner showing yellow on top | Eyes |
| 2 | Slide it to BR using U moves (top-layer only — no whole-cube rotations) | U / U′ / U2 |
| 3 | Apply Sune | R U R′ U R U2 R′ |

Repeat until all 4 yellow. Maximum repetitions: **3 Sunes** (for the 0-yellow starting case).

## Special case: 0 yellow corners

Apply Sune once from any hold. At least one yellow corner will appear. Then run the 3-step loop.

## Non-obvious insights

- `U` moves are safe here: they only rotate the top layer horizontally — they never flip a sticker
  from sideways to upward, so corner orientation is preserved during repositioning.
- BR is the **only** corner Sune leaves untouched. All other corners are cycled/reoriented.
- The 0-yellow case is solved in ≤3 Sunes because each application reduces the number of
  mis-oriented corners (0 → at least 1, then 1 → ...) following a predictable parity pattern.
- Students often confuse this step with the Yellow Cross algorithm (F R U R′ U′ F′).
  The distinguishing feature: Sune has no F moves.

## Zone of proximal development — next

**0007: Permuting Yellow Corners** — all corners are now yellow-up but likely in wrong positions.
Use the Jb/Aa algorithm (or a simplified beginner headlights approach) to cycle corners into place.
