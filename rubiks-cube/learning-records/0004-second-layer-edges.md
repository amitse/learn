# 0004 — Second Layer Edges

**Date:** 2026-06-15
**Lesson:** `0004-second-layer-edges.html`

## What was taught

- After Layer 1, the cube is flipped: **white down, yellow up**. This orientation is held for the rest of the solve.
- The 4 middle-layer slots sit between the four side centres and must be filled with edges that have **no yellow sticker**.
- Any edge with a yellow sticker belongs in the last layer and is skipped in this step.

## The Two Insertion Algorithms

The edge is always set up **above its target slot** (which is positioned at front-right via a y-rotation of the whole cube). Then one of two cases applies, identified by looking at the **side sticker** of the edge (the sticker not on the U face):

| Case | Sign | Algorithm |
|---|---|---|
| A — Right Insert | Front sticker matches front centre | U R U′ R′ U′ F′ U F |
| B — Left Insert  | Right sticker matches right centre | U′ L′ U L U F U′ F′ |

## Extraction pattern

If an edge is already in the middle layer but wrong (wrong orientation or wrong slot):
1. Rotate cube so the mis-inserted edge is at front-right.
2. Apply either insertion algorithm from the top — pops the edge out to U layer.
3. Re-insert it correctly.

## Non-obvious insights

- The "side sticker" rule is the fastest orientation check: no need to memorise two separate setups — just look at the side-facing sticker of the top-layer edge.
- The algorithms are not the same shape as the Right Trigger, but they contain a Right Trigger (R U R′) inside the Right Insert. Students who notice this learn the algorithm faster.
- Left Insert is the mirror of Right Insert — if you can do one, you can derive the other.

## Zone of proximal development — next

**0005: Yellow Cross (OLL step 1)** — orient the top-layer edges so all yellow stickers face up. Introduces the F-move sequence: `F R U R′ U′ F′`.
