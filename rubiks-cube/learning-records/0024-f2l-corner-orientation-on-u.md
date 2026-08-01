# 0024 — F2L: Read Corner Orientation on U

**Date:** 2026-08-01  
**Lesson:** `0024-f2l-corner-orientation-on-u.html`

## What was taught

- An F2L corner’s location on the U layer and its orientation are separate facts: its two non-white colours identify the pair and slot, while the white sticker’s direction identifies whether it matches a known pairing picture.
- White facing Right at UFR and white facing Left at UFL can lead to the existing safe right/left pictures once the edge and centres are checked.
- A white-up corner is not ready for either known picture; `U` turns reposition it but do not reorient it.
- With an empty working slot protected, one short trigger (`R U R′` at front-right or `L′ U′ L` at front-left) can normalize the relationship. The solver must stop, re-identify the corner, and construct a confirmed picture rather than repeat blindly.

## Key non-obvious insights

- “On U” does not mean “ready to pair.” Orientation must be read before selecting a trigger.
- A trigger is controlled orientation/relationship work only when it passes through an empty slot; its result is information to inspect, not a sequence to loop.
- U turns are safe setup tools for top-layer positions, while solved F2L pairs remain protected below.

## Zone of proximal development — next

**F2L edge orientation on U:** distinguish an edge whose front sticker does or does not match the front centre, then use a protected setup to make the edge agree with a known right- or left-facing pairing picture. This extends the same read → normalize → re-read loop from corners to edges.
