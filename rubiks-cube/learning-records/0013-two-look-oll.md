# 0013 — 2-Look OLL: Orient the Last Layer in Two Steps

**Date:** 2026-07-09
**Lesson:** `0013-two-look-oll.html`

## What was taught

- **OLL** defined: Orientation of the Last Layer — making every top-face sticker yellow after F2L
- Why 2-Look OLL exists: full OLL = 57 algorithms; 2-Look OLL achieves the same result with **2 algorithms in ≤ 2 looks**
- **Look 1 = yellow cross** (already covered in Lesson 5): `F (R U R' U') F'`
- **Look 2 = OCLL** (orient corners only) — the 4 practical cases:
  1. All 4 corners yellow → skip, go to PLL
  2. 1 correct corner at back-left → **Sune**: `R U R' U R U2 R'`
  3. 1 correct corner at back-right → **Anti-Sune**: `R' U' R U' R' U2 R`
  4. 0 correct corners → apply Sune from any angle → converts to a 1-corner case
- Setup rule: rotate the **whole cube** (y/y') to place the correct corner at BL (Sune) or BR (Anti-Sune) — never move the U face
- Anti-Sune is a direct mirror of Sune

## Key non-obvious insights

- Moving a U-face sticker to "set up" position would scramble the cross you just made — whole-cube rotation (y) is the correct technique
- The 0-corner case doesn't need a special algorithm: one Sune from any angle always produces a 1-corner case, regardless of orientation
- Maximum 2 algorithm executions for OLL in any scramble — that upper bound is the key efficiency gain over the beginner method
- OCLL (this lesson) is the harder recognition step of 2-Look OLL; the yellow cross (Look 1) is mechanically simpler

## Zone of proximal development — next

- **2-Look PLL**: the final CFOP-entry skill — permuting last-layer edges and corners using just 3 algorithms (T-perm, Y-perm, Ua/Ub-perm). Completes the full 2-Look CFOP framework.
- **Sub-2-minute drill**: after 2-Look OLL + 2-Look PLL are comfortable, timed solve practice with a concrete target gives structured motivation.
- **Recognition speed training**: pattern-match top-face corner count without stopping to think — the bottleneck after algorithm fluency is achieved.
