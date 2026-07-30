# Learning Record 0022 — Test the Graceful-Shutdown Deadline

## Date
2026-07-30

## Lesson
0024 — Test the Graceful-Shutdown Deadline

## What was taught

- `http.Server.Shutdown(ctx)` closes listeners, then waits only until active connections become idle **or** `ctx` is cancelled.
- If an active handler outlives a timeout context, `Shutdown` returns the context error—normally `context.DeadlineExceeded`.
- A deadline return does not forcibly stop the active handler. The listener is already closed, but application work may still be running; callers should treat the result as an operational failure.
- A `started` channel establishes that a request has entered the deliberately blocked handler before shutdown begins. A `release` channel keeps it active without scheduler-dependent sleeps.
- `context.WithTimeout(context.Background(), duration)` supplies the bounded shutdown context; `defer cancel()` releases its timer resources.
- `errors.Is(err, context.DeadlineExceeded)` is the robust assertion because it continues to work when an error is wrapped.
- The test must close `release` after the deadline assertion so its controlled handler can finish rather than leaking work.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `AbortSignal.timeout(duration)` | `context.WithTimeout(context.Background(), duration)` |
| close promise rejects/times out | `srv.Shutdown(ctx)` returns `context.DeadlineExceeded` |
| deferred promise holds a route | `release` channel blocks a handler |
| test waits for route-start event | `<-started` establishes active-request ordering |

## Key insight

A graceful-shutdown deadline is not a mechanism for killing handlers. It is a bounded wait and an explicit signal that the application failed to drain in time. The deterministic test proves this contract by controlling handler lifetime with channels, then releasing it deliberately for cleanup.

## Zone of proximal development notes

Amit has tested both successful shutdown properties: active requests drain (Lesson 22) and new requests are rejected after listener closure (Lesson 23). This lesson completes the lifecycle test triangle by covering the timeout/failure path and reuses familiar contexts, error matching, channels, and ephemeral listener wiring.

## What comes next

- Extract startup, shutdown, and resource cleanup into an application `Run` function with explicit lifecycle ownership.
- Add request-scoped deadlines so slow downstream work can stop before the server-wide shutdown deadline.
- Return to persistence and wire a database pool into that same lifecycle.
