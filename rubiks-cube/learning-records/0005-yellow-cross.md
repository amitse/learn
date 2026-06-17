# 0005 — Yellow Cross (OLL Step 1)

**Date:** 2026-06-17
**Lesson:** `0005-yellow-cross.html`

## What was taught

- After layers 1 and 2 are complete, the solve enters the **Last Layer** phase, broken into OLL (orientation) and PLL (permutation).
- OLL Step 1: orient the 4 top-layer **edge** pieces so all yellow stickers face up — producing the Yellow Cross.
- Yellow **corners** are ignored at this stage.

## The 4 Cases

| Case | Yellow edges up | Setup orientation needed |
|---|---|---|
| Dot  | 0 | None — apply from any hold |
| L-shape | 2 (adjacent) | Yellow edges at Back + Left |
| Bar | 2 (opposite) | Bar runs horizontally (left ↔ right) |
| Cross | 4 | Done — skip to lesson 6 |

Edge parity means **only 0, 2, or 4 yellow edges are ever possible** — never 1 or 3.

## The Algorithm

**F R U R′ U′ F′**

- Starting from a **Dot**: apply once (any hold) → L or Bar, then set up and apply once more → Cross.
- Starting from **L** or **Bar** (correctly oriented): apply once → Cross.
- Worst case: **2 applications**.

The middle 4 moves are the Right Trigger (R U R′ U′) wrapped in F … F′.

## Non-obvious insights

- "Correctly oriented L" means the *gap* of the L faces front-right — useful mnemonic: you're filling the gap with the algorithm.
- The Bar case: thinking of the bar as a line through the cube helps — hold it sideways (horizontal) so the algorithm pushes both ends up symmetrically.
- The F and F′ at the start and end act as a bracket: the F twists the front layer to bring an edge into position, and F′ puts it back after the trigger fires.

## Zone of proximal development — next

**0006: Orienting Yellow Corners** — after the Yellow Cross exists, use `R U R′ U R U2 R′` (Sune algorithm) to flip yellow corners until all 4 face up.
