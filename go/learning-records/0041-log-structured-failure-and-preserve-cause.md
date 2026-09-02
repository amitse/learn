# Learning Record 0041 — Log a Structured Failure and Preserve Its Cause

## Date
2026-09-02

## Lesson
0041 — Log a Structured Failure and Preserve Its Cause

## What was taught

- The application boundary can log an unexpected `Run` failure as a stable `slog` error event while returning an independently useful wrapped error.
- `logger.Error("HTTP server stopped unexpectedly", slog.Any("error", err), ...)` records the failure as a named field alongside process identity attributes.
- `fmt.Errorf("run HTTP server: %w", err)` retains the original error in Go’s error chain; formatting with `%v` or making a new error does not.
- A focused buffer-backed logger test should assert both contracts: expected structured log fragments and `errors.Is(returned, sentinel)`.
- The test needs no real listener: an injected `func() error` deterministically supplies the sentinel failure.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `logger.error({ err }, "server failed")` | `logger.Error("HTTP server stopped unexpectedly", slog.Any("error", err))` |
| `new Error("run server", { cause: err })` | `fmt.Errorf("run HTTP server: %w", err)` |
| inspect `error.cause` | `errors.Is(err, sentinel)` walks the chain |

## Key insight

A log event is operational evidence, not a substitute for returning the failure. Keep the stable structured event for operators and preserve the wrapped cause for callers; one must not weaken the other.

## Zone of proximal development notes

Amit already emits structured startup identity and has application-boundary wrapping from Lesson 32. This lesson joins those established techniques on exactly one new branch—the unexpected failure path—and verifies its two independent contracts.

## What comes next

- Choose whether production should use a text or JSON `slog.Handler` based on the actual log collector.
- Test the successful completion event with the same injected `slog.Logger` pattern, if it is retained as an operational contract.
- Keep request-level logging separate from process lifecycle events.
