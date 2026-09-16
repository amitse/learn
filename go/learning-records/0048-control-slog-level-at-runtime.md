# Learning Record 0048 — Control slog Level at Runtime

## Date
2026-09-16

## Lesson
0048 — Control `slog` Level at Runtime

## What was taught

- `slog.LevelVar` is a concurrency-safe mutable implementation of `slog.Leveler`; a handler consults it when deciding whether to emit a record.
- Initialize one application-owned `LevelVar` from the already validated typed `Config.LogLevel`, then pass its pointer to `slog.HandlerOptions{Level: &level}`.
- Calls such as `level.Set(slog.LevelDebug)` change the shared threshold for subsequently handled records without rebuilding the logger or reading environment variables again.
- A plain `slog.Level` passed to handler options is a fixed selection, while the same `*slog.LevelVar` must be shared between construction and the authorized control path.
- Test a transition through observable output: require a DEBUG event before lowering the level to be absent, then require a later DEBUG event to be present.
- Runtime log-level control is an operational capability, not a request-handler decision. It needs an explicit owner, authentication, and an audit trail; restart-time `LOG_LEVEL` remains the simpler default.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| mutable logger-level controller | application-owned `*slog.LevelVar` |
| transport asks a level provider | `HandlerOptions.Level` accepts a `slog.Leveler` |
| update current threshold in incident control path | `level.Set(slog.LevelDebug)` |
| startup config initializes controller | typed `cfg.LogLevel` initializes the `LevelVar` |

## Key insight

A dynamic threshold is safe only when it is one deliberately shared application dependency: configuration establishes its starting value, the handler reads it, and an authorized operational path changes it. It is not a reason to let handlers reread environment variables or choose logging policy.

## Zone of proximal development notes

Amit already has typed environment parsing and explicit JSON-handler level policy. `LevelVar` is the narrow next step: it preserves that boundary while introducing a standard-library concurrency-safe value and a focused state-transition test, without prematurely designing an admin API.

## What comes next

- Define an authenticated and auditable control boundary only if temporary runtime verbosity is a real operational need.
- Before request logging, name a concrete operator question and distinguish request-event fields from lifecycle-event fields.
- If no runtime control path is justified, retain the simpler restart-time `LOG_LEVEL` configuration policy.
