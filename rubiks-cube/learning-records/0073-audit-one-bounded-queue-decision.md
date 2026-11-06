# 0073 — Audit One Bounded Queue Decision

**Date:** 2026-11-06  
**Lesson:** `0073-audit-one-bounded-queue-decision.html`

## What was taught

- A bounded F2L queue decision is audited in a fixed order: literal checkpoint evidence, copied ACT-row permission, preserved four-field scope, then exactly one action.
- The four carried scope fields are target pair, empty-workspace type, retained cue/timing, and checkpoint.
- The first failed check sets the repair boundary: change only that clause, then re-audit.

## Key non-obvious insights

- Correct later wording cannot compensate for an earlier failure; a front-left workspace still fails even when the evidence and action are otherwise correct.
- “Successful,” “smooth,” and explanations such as a bad grip are conclusions, not literal checkpoint evidence.
- An apparently helpful follow-up action is still out of bounds when the ACT source row authorizes only one action.

## Zone of proximal development — next

**F2L: classify one audited bounded queue decision.** Distinguish an action-ready decision from one needing minimal repair by checking whether every audit field passes after the first-failure repair.
