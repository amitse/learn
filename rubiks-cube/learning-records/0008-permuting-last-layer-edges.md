# 0008 — Permuting Last Layer Edges

**Date:** 2026-06-24
**Lesson:** `0008-permuting-last-layer-edges.html`

## What was taught

- **PLL Step 2**: the final step of the layer-by-layer beginner method
- Diagnose the 4 last-layer edges: check whether each edge's side-facing sticker matches the centre directly below it
- Three possible cases: 4 correct (solved), 1 correct (3-cycle), 0 correct (H/Z-perm → converts to 3-cycle after one Ua)

## The Two Algorithms

| Perm | Algorithm | When to use |
|---|---|---|
| **Ua** | `R U' R U R U R U' R' U' R2` | Front edge belongs to the LEFT slot |
| **Ub** | `R2 U R U R' U' R' U' R' U R'` | Front edge belongs to the RIGHT slot |

Ub is the exact inverse of Ua. Each cycles 3 edges (FL, FR, BL), leaving the back edge fixed.

## Procedure

1. Hold yellow up
2. Count edges whose side sticker matches the centre below
   - 4 correct → solved
   - 0 correct → apply Ua from any hold, re-diagnose (will become 3-cycle)
   - 1 correct → continue
3. Rotate whole cube (y / y′) to place the correct edge at the back
4. Identify: front edge belongs LEFT → Ua; RIGHT → Ub
5. Apply algorithm → all 4 edges are solved

Maximum 2 algorithm applications from any scramble.

## Significance

- **This lesson completes the full beginner layer-by-layer method**
- Amit now has every algorithm and procedure needed for an unaided solve from any scramble
- The 7 stages in order:
  1. White Cross (Daisy method)
  2. White Corners (R U R′ U′ / F′ U′ F)
  3. Second Layer Edges (Right Insert / Left Insert) — flip cube white down
  4. Yellow Cross (F R U R′ U′ F′) — up to 2 applications
  5. Orient Yellow Corners (Sune: R U R′ U R U2 R′) — up to 3 applications
  6. Permute Corners (R U R′ F′ · R U R′ U′ · R′ F · R2 U′ R′) — headlights to back
  7. Permute Edges (Ua / Ub) — this lesson

## Non-obvious insights

- The "0 correct" case is **not an error** — it's a valid parity arrangement. Always apply Ua once first.
- The rule "front edge belongs LEFT → Ua, RIGHT → Ub" is reliable once the anchor edge is at back. The direction of the cycle (counterclockwise / clockwise when viewed from above) mirrors which direction the pieces travel.
- Using `U` moves to reposition during this step is a common mistake — U would only shuffle the edges without fixing them. Always use **whole-cube rotations** (y / y′) to place the anchor edge at back.
- Ub is the inverse of Ua, so a learner who can't recall Ub can apply Ua twice (same net result as one Ub).

## Zone of proximal development — next

**Attempt a full unaided solve.** Work through all 7 stages with a scrambled cube. If specific stages feel shaky, identify which one stalls the solve and review that lesson. After a successful first solve, the next learning arc is:
- Practice tips and common mid-solve mistakes (Lesson 9, planned)
- Finger tricks for fluency
- Introduction to CFOP (only when consistent solves are achieved)
