# 0025 — F2L: Check the Edge Before the Trigger

**Date:** 2026-08-02  
**Lesson:** `0025-f2l-edge-centre-check.html`

## What was taught

- A recognized F2L pair can still be in an unsafe relationship for insertion: pair identity and current edge orientation/relationship are separate checks.
- In either known safe F2L picture, the matching edge is at `UF` and its front-facing sticker must match the front centre.
- A right insertion also requires the corner at `UFR` with white facing Right before `R U R′`; a left insertion requires the corner at `UFL` with white facing Left before `L′ U′ L`.
- When the edge-centre check fails, the solver should protect an empty working slot, use only `U`/`U′`/`U2` to explore a setup, then re-read both the corner direction and edge-centre match before inserting.

## Key non-obvious insights

- The same blue-red (for example) edge remains the correct partner for a white-blue-red corner even when its front-facing sticker is wrong for the current hold; identity is not readiness.
- Fixed centres are the objective reference for edge readiness. A corner that looks ready is only half of a safe pairing picture.
- The edge-centre check is a deliberate stop signal: forcing a trigger after it fails turns a recognition problem into a recovery problem.

## Zone of proximal development — next

**F2L edge normalization on U:** take an edge whose front sticker fails the centre check, deliberately change its corner–edge relationship through an empty working slot, then re-read and build one confirmed right- or left-facing pairing picture. This develops controlled edge orientation work without introducing a large case list.
