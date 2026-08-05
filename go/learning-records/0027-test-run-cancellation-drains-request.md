# Learning Record 0027 — Test Run Drains an Active Request

## Date
2026-08-05

## Lesson
0027 — Test Run Drains an Active Request

## What was taught

- A graceful-shutdown lifecycle test must prove an ordering: an active handler starts, shutdown begins, `Run` remains pending, then the handler is released and `Run` returns successfully.
- A `started chan struct{}` closed by the handler gives deterministic evidence that the request has entered the active state before the test cancels the stop context.
- A `release chan struct{}` deliberately blocks that handler, making the test—not scheduler timing—control when the active request can complete.
- Run `Run` in a goroutine and receive its terminal error through a buffered `chan error`, then use a `select` with `default` to assert it has not returned prematurely.
- Close `release` only after that assertion and require `Run` to return `nil`; this verifies that its `Shutdown` path drains the request.
- `time.Sleep` is not synchronization. The server grace timeout remains a production safety bound, while channels are the test's event-order evidence.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| deferred Promise resolved at handler entry | `started chan struct{}` closed at handler entry |
| deferred Promise which holds an async handler | `release chan struct{}` received by the handler |
| inspect whether server close promise settled | non-blocking `select` on `runDone` |
| `AbortController.abort()` | `cancel()` on a stop context |

## Key insight

A deterministic concurrent test does not measure whether enough time has passed. It arranges observable state transitions and asserts their order. `started` and `release` turn graceful shutdown from a timing guess into a directly testable contract.

## Zone of proximal development notes

Amit already understands active-request drain tests, non-blocking selects, `Run` lifecycle ownership, and cancellation-driven shutdown. This lesson composes those pieces at the public lifecycle boundary: it tests the production `Run` behavior rather than `http.Server.Shutdown` in isolation.

## What comes next

- Test that `Run` rejects a new request after cancellation while an active request is still draining.
- Add a narrow seam that makes an unexpected `Serve` failure independently testable.
- Close dependent resources (such as a database pool) only after `Run` reports drained HTTP shutdown.
