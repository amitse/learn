# 0014 — 2-Look PLL: Permute the Last Layer in Two Steps

**Date:** 2026-07-11
**Lesson:** `0014-two-look-pll.html`

## What was taught

- **PLL** defined: Permutation of the Last Layer — moving last-layer pieces into their correct positions after OLL
- Why 2-Look PLL: full PLL = 21 algorithms; 2-Look PLL covers every case with **3 algorithms in ≤ 4 executions**
- **Look 1 = Corner Permutation** using Y-Perm: `F R U' R' U' R U R' F' R U R' U' R' F R F'`
  - Places matched corner pair at back → Y-Perm cycles the other 3
  - Diagonal swap (no matching pair) → apply Y-Perm from any angle → creates matching pair → apply again
- **Look 2 = Edge Permutation** using Ua/Ub-Perm:
  - Ua-Perm (CCW cycle): `R U' R U R U R U' R' U' R2`
  - Ub-Perm (CW cycle): `R2 U R U R' U' R' U' R' U R'`
  - Always place the solved edge at the back
  - Front sticker goes left → Ua; front sticker goes right → Ub; or just apply Ua twice

## Key non-obvious insights

- The diagonal corner swap is NOT a special case — Y-Perm from any angle always resolves it into a 1-matching-pair scenario
- Worst case for 2-Look PLL = 4 algorithm executions (2 corner algs + 2 edge algs), not 2
- Turning U to "find" a matching pair will unseat the OLL orientation — always use whole-cube y rotations
- Ua-Perm applied twice achieves the Ub-Perm effect — can avoid memorising recognition entirely at first
- With 2-Look OLL + 2-Look PLL, the complete CFOP beginner framework is now available for unaided solves

## Zone of proximal development — next

- **First timed solve session**: armed with full 2-Look OLL + 2-Look PLL, attempt a complete solve from any scramble and time it
- **Algorithm fluency drilling**: isolate each of the 5 key algorithms (Sune, Anti-Sune, Y-Perm, Ua, Ub) and drill them until they're finger-muscle memory
- **Sub-3-minute target**: realistic first milestone with the beginner CFOP framework; use a timer app (cstimer.net) for structured practice
