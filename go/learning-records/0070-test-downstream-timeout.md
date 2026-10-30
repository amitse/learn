# Learning Record 0070 — Test a Downstream Timeout

## Date
2026-10-30

## Lesson
0070 — Test a Downstream Timeout

## What was taught

- A dependency timeout is a child context derived with `context.WithTimeout(parent, duration)`; it adds a local time budget while retaining cancellation from the incoming request.
- Call the returned `cancel` function with `defer cancel()` to release resources associated with the deadline when the operation completes first.
- Pass that child context through `http.NewRequestWithContext`, so the HTTP client and transport observe its deadline.
- A deterministic transport fake signals `started`, waits on `req.Context().Done()`, and returns `req.Context().Err()`; the test asserts `errors.Is(err, context.DeadlineExceeded)`.
- A timeout and explicit cancellation can both stop work but have different causes: `context.DeadlineExceeded` versus `context.Canceled`.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `AbortSignal.timeout(2000)` composed with request signal | `context.WithTimeout(request.Context(), 2*time.Second)` |
| timeout/abort rejection | `context.DeadlineExceeded` / `context.Canceled` |
| `fetch(url, { signal })` | `http.NewRequestWithContext(ctx, ...)` |

## Key insight

The dependency deadline must be a child of the caller’s context, not a fresh background context. That preserves the caller’s right to stop work while preventing one slow dependency from consuming the entire request lifetime.

## Zone of proximal development notes

Amit already proved that caller cancellation reaches a blocked dependency request (lesson 69), and its transport error remains recognizable through wrapping (lesson 68). This lesson changes only the source of the stop signal to a child deadline, reusing the same deterministic blocking-transport pattern and avoiding retry policy or client frameworks.

## What comes next

- Define a narrow policy for retryable dependency failures, keeping cancellation and deadline exhaustion non-retryable by default.
- Map dependency failures to the safe public JSON error contract while retaining private operational context.
- Test a selected retryable transport failure separately from deadline and cancellation errors.
