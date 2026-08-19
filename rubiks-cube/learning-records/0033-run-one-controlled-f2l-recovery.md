# 0033 — F2L: Run One Controlled Recovery

**Date:** 2026-08-19  
**Lesson:** `0033-run-one-controlled-f2l-recovery.html`

## What was taught

- A controlled F2L recovery is a closed decision cycle: read the failed check, select one bounded action, execute it once through an empty working slot, then re-read from pair identity.
- For an edge-readiness or corner-orientation failure when no different pair is ready, one short trigger through the empty working slot is a normalization move—not insertion authorization.
- The recovery ends with a fresh observation; an unfamiliar post-trigger picture is a reason to re-read, never a reason to chain a second trigger.
- If a different pair is fully green-lit during the re-read, inserting it is less disruptive than continuing to work on the unready pair.

## Key non-obvious insights

- A recovery can succeed without solving the target pair: its success condition is a legible cube state and a new evidence-based next decision.
- Observations made before normalization expire after the trigger; piece identity, slot, and both green-light checks must be established again.
- The empty working slot is a hard boundary that makes recovery reversible enough to reason about while protecting completed pairs.

## Zone of proximal development — next

**F2L: choose between a named U setup and recovery after a fresh re-read.** Given the post-normalization state, distinguish a viable positional setup from an unready relationship and name the one valid next action without trial turns.
