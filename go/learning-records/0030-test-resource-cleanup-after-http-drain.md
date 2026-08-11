# Learning Record 0030 — Test Resource Cleanup After HTTP Drains

## Date
2026-08-11

## Lesson
0030 — Test Resource Cleanup After HTTP Drains

## What was taught

- Resource cleanup is a lifecycle-order contract: an HTTP dependency must remain usable until active handlers have drained and `Run` has reached its terminal result.
- A small fake resource with a `closed chan struct{}` makes cleanup observable without a database or timing guesses.
- `sync.Once` makes the fake's `Close` method idempotent, so test cleanup cannot panic if it closes the resource more than once.
- A `started` channel proves an HTTP handler is active; a `release` channel holds it there under test control.
- Before `release` is closed, a non-blocking `select` must prove the resource has not been closed.
- After `release`, `Run` must return before application-edge cleanup closes the resource; a second assertion proves closure then occurred.

## TS → Go comparison

| TypeScript | Go |
|---|---|
| Deferred Promise at route entry | `started chan struct{}` closed by the handler |
| Fake `db.end()` records cleanup | fake `Close()` closes an observation channel |
| Check a promise is not settled | non-blocking `select` with `default` |
| `await server.close(); await db.end()` | wait for `Run`, then call `resource.Close()` |

## Key insight

Listener closure only stops new work. It does not make a shared dependency safe to close: an already-admitted handler may still need it. The correct test asserts both sides of the boundary—**not closed while work is active; closed after the HTTP lifecycle returns**.

## Zone of proximal development notes

Amit has already built deterministic tests for `Run` cancellation, admission closure, and active-request draining, and has placed pool cleanup after `Run`. This lesson combines those established primitives into a focused integration test of application-level resource ordering.

## What comes next

- Turn the outline into a compiling `Run` + application-edge integration test with a narrow resource interface.
- Decide and test the policy for resource cleanup when shutdown returns a deadline error.
- Add a narrow seam to test an unexpected `Serve` failure independently of intentional shutdown.
