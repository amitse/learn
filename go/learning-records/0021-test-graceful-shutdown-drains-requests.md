# Learning Record 0021 — Test Graceful Shutdown Drains Requests

## Date
2026-07-26

## Lesson
0022 — Test Graceful Shutdown Drains Requests

## What was taught

- `http.Server.Shutdown(ctx)` closes the listener and waits for active connections to become idle; it either returns after draining or when its context expires.
- A shutdown test must coordinate request state deterministically. A `started` channel tells the test the handler has entered; a `release` channel keeps the handler active until the test permits it to finish.
- Start both the client request and `Shutdown` in goroutines because both can block while the handler is deliberately held.
- A non-blocking `select` on the shutdown result immediately before releasing the handler verifies the crucial property: shutdown has not returned early.
- Closing `release` unblocks the handler; it writes its response, then shutdown can return successfully.
- Use timeouts only as a final guard against a hung test, not to guess that a handler has started. Channels establish causal ordering without scheduler-dependent sleeps.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| deferred promise resolved when route starts | `started := make(chan struct{}); close(started)` |
| deferred promise awaited inside route | `<-release` inside handler |
| `server.close()` promise pending during active request | `done <- srv.Shutdown(ctx)` pending during active handler |
| `await release` to continue request | `close(release)` to unblock handler |

## Key insight

A graceful-shutdown test is not a timing test. It is a small concurrency proof: establish that a request is active, verify shutdown remains pending, then release the request and observe completion. Channels make each transition explicit and deterministic.

## Zone of proximal development notes

Amit already understands graceful shutdown, contexts, goroutines, and ephemeral listener wiring. This lesson joins them in a real-network test and adds one carefully scoped channel coordination pattern. It advances the server from “has shutdown code” to “proves it preserves an in-flight response.”

## What comes next

- Test listener closure separately: after shutdown begins, a fresh connection/request must no longer be accepted while the already-active request drains.
- Or return to persistence startup/shutdown wiring with `pgxpool`, applying the same explicit lifecycle approach.
