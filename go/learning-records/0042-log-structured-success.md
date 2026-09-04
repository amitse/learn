# Learning Record 0042 — Log Structured Success Without Changing Success

## Date
2026-09-04

## Lesson
0042 — Log Structured Success Without Changing Success

## What was taught

- A clean lifecycle completion can be recorded at the application boundary with `logger.Info("HTTP server stopped cleanly", ...)` only after the injected `Run` function returns `nil`.
- The success event carries the same named `version` and `revision` identity attributes as startup and failure events, while retaining a stable message suitable for querying.
- Logging normal completion must not alter the success contract: the wrapper returns `nil`, rather than manufacturing or wrapping an error.
- A focused test injects a `func() error` that returns `nil` and a buffer-backed `slog.Logger`, then asserts invocation, a `nil` return, and the INFO message plus identity-field fragments.
- The completion event describes the lifecycle owner’s normal terminal outcome; it does not belong in individual request handlers and does not claim every request outcome.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `await run(); logger.info({ version, revision }, "server stopped cleanly")` | `if err := run(); err != nil { ... }; logger.Info("HTTP server stopped cleanly", ...)` |
| resolved `Promise<void>` remains resolved | successful wrapper returns `nil` unchanged |
| logger directed to an in-memory test transport | `slog.NewTextHandler(&bytes.Buffer{}, nil)` |

## Key insight

A clean-stop log is operational evidence of a successful lifecycle result, not a replacement for that result. Emit it only after `Run` returns `nil`, and preserve `nil` so callers can still compose the lifecycle honestly.

## Zone of proximal development notes

Amit already has a transparent success path (Lesson 33), structured startup identity (Lesson 40), and a structured failure branch that preserves causes (Lesson 41). This lesson completes the smallest symmetrical lifecycle contract: a testable structured success event without reopening shutdown mechanics or adding request-level logging.

## What comes next

- Choose the production `slog.Handler` representation (text versus JSON) based on the deployment’s log collector.
- Add handler-level request logging only if it has a concrete operational question and separate it from lifecycle events.
- Consider a narrow readiness/identity endpoint only when operators require HTTP-visible identity, not as a substitute for logs.
