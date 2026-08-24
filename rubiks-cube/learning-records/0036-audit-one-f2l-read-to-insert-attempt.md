# 0036 — Audit One F2L Read-to-Insert Attempt

**Date:** 2026-08-24  
**Lesson:** `0036-audit-one-f2l-read-to-insert-attempt.html`

## What was taught

- A one-line F2L observation log records the plan, the first observed mismatch, its label, and one change for the next attempt.
- A planning error occurs before expected destination positions: pair/slot/side identification or named setup was wrong.
- A green-light failure occurs after correct destination positions when either the corner white-direction or edge-centre check fails; it blocks insertion and calls for a fresh read or one bounded normalization.
- An execution error is diagnosed only when the full green light was present before the authorized trigger, but the trigger did not yield its expected result.

## Key non-obvious insights

- The first mismatch is the useful one. Later observations may be consequences, so logging them as the cause makes the next practice attempt unfocused.
- Reaching `UFR + UF` or `UFL + UF` proves only positional success; it does not convert a White-Up corner or unready edge into an insertion case.
- A bounded audit supports the mission’s recovery goal: it changes one decision or physical action without sacrificing protected F2L pairs to exploratory turns.

## Zone of proximal development — next

**F2L: compare two observation logs and choose one targeted micro-drill.** Identify whether repeated evidence points to pair/side planning, green-light recognition, or trigger execution, then practise only that bottleneck for several controlled attempts.
