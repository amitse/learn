# Learning Record 0069 — Test Downstream Context Cancellation

## Date
2026-10-28

## Lesson
0069 — Test Downstream Context Cancellation

## What was taught

- An outbound dependency request must be created with the caller’s context, so cancellation of the HTTP work reaches the dependency boundary.
- A small `http.RoundTripper` fake can signal that it has started, wait on `req.Context().Done()`, and return `req.Context().Err()`.
- A `started` channel makes the test deterministic: cancel only after the fake is blocked; do not use `time.Sleep` to guess that state.
- The adapter’s error wrapper must retain `context.Canceled`, allowing `errors.Is(err, context.Canceled)` to verify cancellation at the observable boundary.
- This test proves local context propagation and error identity, not remote-server cancellation delivery or retry behavior.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `AbortController` / `signal` | `context.WithCancel` / `ctx` |
| `controller.abort()` | `cancel()` |
| wait for a test dependency to start | wait for `<-started` |
| rejected aborted `fetch` | `errors.Is(err, context.Canceled)` |

## Key insight

Cancellation tests should synchronize on a meaningful state transition, not elapsed time. Once the fake transport says it is waiting, cancellation verifies the context path from application work to the outbound request.

## Zone of proximal development notes

Amit already creates an outbound request with context (lesson 67) and wraps a transport error while preserving its cause (lesson 68). This lesson combines those two local contracts in one deterministic test without introducing retries, deadlines, or a client framework.

## What comes next

- Test a dependency timeout as distinct from explicit cancellation using `context.WithTimeout` and `context.DeadlineExceeded`.
- Define a narrow policy for which dependency failures are retryable, separately from cancellation.
- Map dependency failures to the safe public JSON error contract while preserving private diagnostic context.
