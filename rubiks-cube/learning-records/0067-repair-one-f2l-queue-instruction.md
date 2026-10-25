# 0067 — Repair One F2L Queue Instruction

**Date:** 2026-10-25  
**Lesson:** `0067-repair-one-f2l-queue-instruction.html`

## What was taught

- A failed F2L queue instruction is repaired against its selected audited-card row in the fixed order: **classification → scope → one action**.
- The repair boundary is the first failure only: restore a wrong ACT/FRESH EVIDENCE label, copy a drifted scope field, or remove an extra/unpermitted action.
- A queue instruction must preserve the source row’s target pair, empty-workspace type, retained cue, and checkpoint.
- The repaired line still has exactly one permitted cube-side action: an ACT drill or a FRESH EVIDENCE matching replacement attempt.

## Key non-obvious insights

- Correct later wording does not cure an earlier error. If classification fails, do not edit scope or action first.
- Minimal repair protects usable parts of an instruction and keeps the next physical cube attempt tied to one interpretable condition.
- “Then…” is often a second-action warning: an otherwise correct ACT drill does not also authorize collecting replacement evidence.

## Zone of proximal development — next

**F2L: execute one repaired queue instruction and log its single checkpoint.** Use the corrected row-local instruction without changing conditions mid-attempt, then record the stated observation before drawing a new conclusion.
