# Learning Record 0028 — Test Run Rejects New Requests

## Date
2026-08-07

## Lesson
0028 — Test Run Rejects New Requests

## What was taught

- `Run`’s graceful-shutdown contract has two independent, compatible outcomes: active work drains while new TCP connections are rejected.
- Cancellation is a shutdown trigger, not proof that admission has stopped. A `net.Listener` wrapper that observes `Close` provides the exact test boundary.
- Embed `net.Listener` in a test wrapper and override only `Close`; use `sync.Once` so the observation channel is safely closed once even when cleanup closes the listener again.
- A client test must force a fresh connection. `http.Transport{DisableKeepAlives: true}` prevents keep-alive reuse from masking an admission bug.
- The complete deterministic order is: establish active request → cancel `Run` → observe listener closure → assert a fresh request fails → release active handler → require `Run` to return `nil`.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| observe `server.close()` callback/spying | wrapper observes `net.Listener.Close()` |
| fresh agent/connection tests closed admission | `Transport{DisableKeepAlives: true}` |
| existing request completes during `server.close()` | active handler drains during `srv.Shutdown(ctx)` |

## Key insight

A graceful lifecycle test needs an observable admission boundary. Testing immediately after `cancel()` is a race because `Run` may not have called `Shutdown` yet; waiting for the wrapped listener’s `Close` turns “new requests are rejected” into a precise, reproducible contract.

## Zone of proximal development notes

Amit previously tested listener closure and active-request draining independently, then tested `Run` draining an active request. This lesson composes those established primitives at the public `Run` boundary and introduces no new production abstraction.

## What comes next

- Add an explicit resource-cleanup phase: close a database pool only after `Run` has drained HTTP work.
- Add a narrow seam to test an unexpected `Serve` failure separately from intentional shutdown.
- Extract the listener-observation pattern into a package-local `_test.go` helper.
