# Learning Record 0029 — Close Resources After HTTP Drains

## Date
2026-08-09

## Lesson
0029 — Close Resources After HTTP Drains

## What was taught

- Graceful shutdown has a dependency-order contract: the HTTP server stops admission and drains active handlers before resources used by those handlers are closed.
- `Run` remains the HTTP lifecycle owner; the application edge (`main`) owns resources that surround it, such as a `pgxpool.Pool`.
- Put `defer pool.Close()` immediately after successful pool construction in `main`; when `main` waits for `Run` and returns, deferred cleanup occurs after the drain.
- Closing a database pool before `Run` returns can break an active handler that graceful shutdown intentionally permits to finish.
- This is an ordering policy, not a reason to give handlers responsibility for closing shared dependencies.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `await server.close(); await db.end()` | `if err := Run(...); err != nil { ... }; pool.Close()` |
| application composition root owns server and DB | `main` constructs the pool, injects it into routes, and defers cleanup |
| close waits for in-flight requests | `Run` returns only after `Shutdown` has drained or failed |

## Key insight

A resource used by an in-flight request must outlive the HTTP drain. The reliable shutdown order is: trigger stop → close admission → drain active work → return from `Run` → close dependencies.

## Zone of proximal development notes

Amit has established and tested `Run`'s cancellation, admission, and drain contracts. This lesson makes the next production composition step without adding a new concurrency primitive: it places the already-understood lifecycle boundary around a shared database dependency.

## What comes next

- Write a deterministic integration test with a fake resource that proves cleanup occurs after a blocked handler is released.
- Add a narrow seam to independently test an unexpected `Serve` failure.
- Decide and document the policy for a shutdown deadline error: whether dependency cleanup still proceeds during process exit.
