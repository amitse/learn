# 0075 — Write One Action-Ready Queue Decision

**Date:** 2026-11-11  
**Lesson:** `0075-write-one-action-ready-queue-decision.html`

## What was taught

- An action-ready F2L queue decision is assembled from four bounded parts: literal checkpoint evidence, copied ACT-row action, preserved four-field scope, and an explicit one-action stop.
- The valid checkpoint log supplies only the visible observation; the selected ACT row supplies only the permitted action.
- A finished decision is ready to execute only when every phrase can be traced back to the log or source row and no follow-up action is appended.

## Key non-obvious insights

- A precise decision does not need an explanation of why the cube state occurred; "smooth," "bad grip," and similar conclusions cannot replace literal evidence.
- Scope is not decorative context: the pair, empty workspace, cue/timing, and checkpoint make an action comparable to the source attempt.
- An otherwise correct decision becomes unready when it adds an apparently useful insertion after the authorized drill.

## Zone of proximal development — next

**F2L: audit one freshly written action-ready queue decision.** Compare a new decision with its checkpoint log and ACT row in the fixed order—literal evidence, copied permission, four-field scope, exactly one action—and identify the first minimal repair if it fails.
