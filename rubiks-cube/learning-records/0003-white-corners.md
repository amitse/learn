# 0003 — White Corners

**Date:** 2026-06-13
**Lesson:** `0003-white-corners.html`

## What was taught

- White corners complete Layer 1 (after the white cross from Lesson 2)
- Each of 8 corners has 3 stickers; its home slot is identified by matching the 2 non-white sticker colours to the 2 adjacent **centre** pieces
- Centres are fixed — they are the permanent colour compass throughout the solve

## The Right Trigger (R U R′ U′)

The **Right Trigger** is the most important beginner algorithm. Used here for corner insertion, and again for middle-layer edges (Lesson 4+).

| Common name | Moves |
|---|---|
| Right Trigger | R U R′ U′ |
| Front Trigger | F′ U′ F |

## 3 Cases

Corner is above its target slot; look where the **white sticker** points:

| Case | White sticker faces | Algorithm |
|---|---|---|
| 1 | Right | R U R′ U′ (1 trigger) |
| 2 | Front | F′ U′ F |
| 3 | Up (away from slot) | R U R′ U′ × 3 continuously |

## Extraction pattern

If a white corner is stuck in the **bottom layer** (wrong slot or orientation):
1. Rotate whole cube so stuck corner is at front-right-bottom
2. Apply `R U R′` to extract it to the top layer
3. Then position and insert normally

## Non-obvious insights

- Case 3 (white up) requires 3 full Right Triggers **in one continuous sequence** — no pausing between repetitions.
- The beginner learner's biggest stumbling block is mis-identifying the case. The key is: look at the white sticker _only_, not the other sticker colours.
- You can rotate the whole cube to bring each target slot to front-right — this keeps the same algorithm for every corner.

## Zone of proximal development — next

**0004: Second Layer Edges (F2L beginner)** — insert the 4 middle-layer edge pieces. Uses the Right Trigger (already learned) with an added U setup, and its mirror.
