# 0078 — Repair One Minimally Classified Queue Decision

**Date:** 2026-11-16  
**Lesson:** `0078-repair-one-minimally-classified-queue-decision.html`

## What was taught

- A queue decision classified **MINIMAL REPAIR NEEDED** is repaired by changing only its earliest failed audit clause.
- Audit order remains literal checkpoint evidence, copied ACT-row permission, four-field scope, then exactly one action.
- After the one-clause repair, the whole decision must be re-audited from the first gate before it can be classified ACTION-READY.

## Key non-obvious insights

- A correct later clause never cures an earlier audit failure, and an apparently helpful rewrite can create fresh unsupported content.
- Repairing scope means restoring the exact carried field; it does not license a new pair, workspace, cue, checkpoint, or cube plan.
- Removing an appended follow-up action is a valid minimal repair when the authorized action already appears correctly; the decision must still stop after that action.

## Zone of proximal development — next

**F2L: classify one re-audited repaired queue decision.** Given a minimally repaired decision and its complete re-audit, decide ACTION-READY or MINIMAL REPAIR NEEDED without executing or expanding the instruction.
