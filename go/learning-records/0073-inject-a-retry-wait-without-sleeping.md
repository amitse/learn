# Learning Record 0073 — Inject a Retry Wait Without Sleeping

## Date
2026-11-04

## Lesson
0073 — Inject a Retry Wait Without Sleeping

## What was taught

- A retry delay is an observable policy step between attempts, so the wrapper accepts it as a named `func(context.Context) error` dependency rather than calling `time.Sleep` internally.
- The wait occurs exactly once only after the first approved operation returns a retryable error; successful and non-retryable first attempts bypass it.
- The wait receives the same caller context as both dependency attempts. If it returns an error such as `context.Canceled`, the wrapper returns that error and never begins attempt two.
- An immediate fake wait plus an event list (`get`, `wait`, `get`) tests order deterministically without a server, elapsed-time assertion, or sleep.
- This establishes a test seam, not a complete delay policy: duration selection, timer implementation, exponential backoff, and jitter remain separate concerns.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| injected `(signal) => Promise<void>` | injected `func(context.Context) error` |
| fake resolved promise records its call | fake function appends `"wait"` and returns `nil` |
| reject/abort during wait stops retry | non-nil wait error returns before second `get` |

## Key insight

A retry wait is a gate, not a promise that another request will happen. Keeping it cancellable and injected preserves the caller’s stop signal and makes the precise call sequence testable immediately.

## Zone of proximal development notes

Amit already has an explicitly safe two-attempt read (72) and distinguishes cancellation/deadline errors (70–71). This lesson changes only the middle step: an injected wait that can be observed and failed, deliberately postponing real timer mechanics and retry-count expansion.

## What comes next

- Implement a production timer-backed wait that returns promptly when its context is done.
- Select a bounded delay policy explicitly, keeping duration and jitter separate from the retry wrapper.
- Preserve an exhausted retry outcome distinctly at the HTTP boundary.
