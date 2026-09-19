# Learning Record 0050 — Serialize Runtime Log-Level Changes

## Date
2026-09-19

## Lesson
0050 — Serialize Runtime Log-Level Changes

## What was taught

- `slog.LevelVar` supports concurrent reads and updates, but that safety applies to individual value operations rather than a multi-step operational action.
- Two simultaneous “read current level → set next level → emit audit” calls can otherwise both record the same prior level, creating an incoherent audit history without a data race.
- Put a `sync.Mutex` inside the one application-owned `LogLevelControl` and hold it across `Level`, `Set`, and synchronous audit submission.
- The lock orders changes made through that control while ordinary HTTP handlers and the application logger continue to read the shared `LevelVar` concurrently.
- Test the rule as an observable transition chain: after two concurrent requests complete, emitted records must join (`second.from == first.to`), beginning with the configured level and ending at the control's resulting level. Do not assert which goroutine wins.
- In-process serialization does not promise that a remote, buffered, or distributed audit sink persists events in that order; the destination must explicitly provide any needed retention-order guarantee.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| async mutex around a level-change transaction | `sync.Mutex` inside `LogLevelControl` |
| read → update → audit in one exclusive callback | `Lock`, `Level`, `Set`, `InfoContext`, `Unlock` |
| concurrent `Promise.all` test, order-independent assertion | goroutines plus `sync.WaitGroup`, chain assertion |

## Key insight

Concurrency-safe mutation and an ordered operational history are separate guarantees. Make the whole level-change-and-audit action serial at its single owner, then be explicit about any ordering the eventual audit sink cannot guarantee.

## Zone of proximal development notes

Amit already has a shared `LevelVar`, a narrow owner, and an independent audit logger. The stated limitation in Lesson 49—simultaneous updates need explicit serialization—is now addressed using one standard-library mutex and an order-independent concurrent test, without introducing an admin transport or expanding logging into request handlers.

## What comes next

- Decide whether runtime log-level changes are a real operational need; prefer restart-time configuration when they are not.
- If they are needed, define an authenticated and authorized operations transport that delegates only to this control.
- Agree with deployment operators on an audit destination and retention/order requirements before claiming a durable audit trail.
