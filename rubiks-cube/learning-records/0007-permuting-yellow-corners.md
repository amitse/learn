# 0007 — Permuting Yellow Corners

**Date:** 2026-06-22
**Lesson:** `0007-permuting-yellow-corners.html`

## What was taught

- After OLL (corner orientation), top-layer corners are yellow-up but may be in wrong positions
- **PLL Step 1**: use the corner permutation algorithm to cycle corners into their correct slots
- The key diagnostic is **headlights** — two matching corner stickers on the same face's top strip
- Corners only; top-layer edges remain scrambled until Lesson 8

## The 3 Cases

| Case | What you see | Action |
|---|---|---|
| Solved | All corners match adjacent centres | Skip to Lesson 8 |
| Adjacent | Headlights on exactly 1 face | Rotate headlights to back, apply algorithm once |
| Diagonal | No headlights on any face | Apply algorithm from any hold once → headlights appear → adjacent case |

Maximum applications from any state: **2**.

## The Corner Permutation Algorithm

**R U R′ F′ · R U R′ U′ · R′ F · R2 U′ R′**

Grouped by purpose:

| Group | Moves | Role |
|---|---|---|
| Setup | R U R′ F′ | Opens corner cycle, F′ creates space |
| Right Trigger | R U R′ U′ | The familiar trigger from Lesson 3 |
| Undo setup | R′ F | Mirrors F′ to close bracket |
| Close | R2 U′ R′ | Drops corners into correct slots |

**Key linkage:** The Right Trigger (R U R′ U′) from Lesson 3 appears verbatim in positions 5–8. Students who notice this accelerate memorisation.

## Headlights concept

- Hold yellow up. Examine the **top strip** of each face: the two corner-sticker squares at top-left and top-right.
- If both squares are the **same colour** → headlights.
- The algorithm preserves the back-right corner and cycles FL, FR, BL corners.
- Headlights at the back = back-right corner is already correctly paired, the other three will fall into place.

## Non-obvious insights

- The diagonal case is valid — OLL did not go wrong. It arises from a legal parity arrangement.
- U moves (top-layer spins) must **not** be used to bring headlights to the back — use a whole-cube y rotation. U moves would scramble the top-layer edge state (though these will be fixed in L8, it's better practice to avoid the confusion).
- After a successful application, check all 4 side strips before moving on — occasionally students mis-identify the headlights face and apply with incorrect orientation.

## Zone of proximal development — next

**0008: Permuting Last Layer Edges** — the final step. All four top-layer edges are placed correctly using the A-perm or a simple edge 3-cycle. After this lesson, Amit can perform a full unaided solve from any scramble.
