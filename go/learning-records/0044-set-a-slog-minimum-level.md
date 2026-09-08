# Learning Record 0044 — Set a slog Minimum Level

## Date
2026-09-08

## Lesson
0044 — Set a slog Minimum Level

## What was taught

- A `slog.Handler` decides whether a log record is enabled before rendering it.
- `slog.HandlerOptions{Level: slog.LevelInfo}` makes an INFO minimum threshold explicit at logger construction.
- At an INFO threshold, INFO, WARN, and ERROR records are emitted; DEBUG records are suppressed.
- The default handler threshold is INFO, but setting it explicitly documents the operational policy at the application boundary.
- `slog.New(slog.NewJSONHandler(os.Stderr, opts))` combines the existing JSON representation with this filtering policy without changing lifecycle log messages or attributes.
- A focused test should capture handler output and assert both sides of the contract: a DEBUG message is absent and an INFO message is present.
- Call sites choose the event’s meaning with `Debug`, `Info`, `Warn`, or `Error`; the composition root chooses the production output threshold.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| logger transport configured with level `"info"` | `slog.HandlerOptions{Level: slog.LevelInfo}` |
| debug event excluded by transport | `logger.Debug(...)` rejected by handler threshold |
| bootstrap constructs one production logger | application boundary constructs one `slog.Handler` |

## Key insight

Log level is deployment policy, not request-handler policy. Set the threshold once on the handler so event-producing code can remain honest about severity while production output stays intentionally useful.

## Zone of proximal development notes

Amit already has JSON lifecycle events with stable messages, version, and revision attributes. Selecting the handler’s minimum level is the smallest next operational refinement: it preserves every established lifecycle contract while adding one observable policy that can be tested without a server.

## What comes next

- Add source locations with `slog.HandlerOptions{AddSource: true}` only if they are useful to the production log consumer, and test the resulting structured field without depending on a line number.
- If request-level logging has a concrete operational question, define one separate request event and choose its level deliberately.
- Keep deployment-specific level overrides at configuration/composition, not scattered through handlers.
