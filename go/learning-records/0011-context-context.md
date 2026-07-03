# Learning Record 0011 — context.Context in HTTP Handlers

## Date
2026-07-03

## Lesson
0012 — context.Context in HTTP Handlers

## What was taught

- **`context.Context` interface**: Four methods — `Deadline()`, `Done()`, `Err()`, `Value()`. The only one regularly checked directly is `Done()` — a channel that's closed when the context is cancelled.
- **`r.Context()`**: Every `http.Request` carries a context automatically populated by `net/http`. Cancelled when the client disconnects or the server shuts down.
- **Passing context downstream**: Convention is `ctx` as the first argument to any function that does I/O. Standard library DB drivers, `http.NewRequestWithContext`, etc. all accept a context and stop work when it's cancelled.
- **`context.WithTimeout`**: Wraps a parent context with a deadline. Returns a derived context and a `cancel` function. The parent (e.g. `r.Context()`) is also respected — if the request is cancelled before the timeout, the derived context cancels too.
- **`defer cancel()`**: Always required after `WithTimeout`/`WithCancel` to release the internal goroutine. Pattern: `ctx, cancel := context.WithTimeout(parent, dur); defer cancel()`.
- **Non-blocking cancellation check**: `select { case <-ctx.Done(): ...; default: }` — checks without blocking, use between expensive steps.
- **`context.WithValue`**: Inject request-scoped data (e.g. authenticated user ID) in middleware; retrieve in handlers with a type assertion. Must use a private named key type to avoid cross-package collisions.
- **TS analogy**: `AbortController` / `AbortSignal` is the closest equivalent. `WithTimeout` ≈ `AbortSignal.timeout()`, `ctx.Done()` ≈ `signal.addEventListener("abort", ...)`.

## TS → Go comparisons taught

| TypeScript | Go |
|---|---|
| `new AbortController()` | `context.WithCancel(ctx)` |
| `controller.signal` | `ctx` (first arg by convention) |
| `signal.aborted` | `ctx.Err() != nil` |
| `signal.addEventListener("abort", fn)` | `select { case <-ctx.Done(): }` |
| `AbortSignal.timeout(2000)` | `context.WithTimeout(ctx, 2*time.Second)` |
| No direct equivalent | `context.WithValue(ctx, key, val)` |

## Key insight

Go's `context.Context` is the primary mechanism for request-scoped cancellation, timeouts, and values — and it flows explicitly through the call stack as a first argument. In TypeScript/Node you'd typically handle timeouts per-call or rely on framework abstractions; in Go, one context cancellation ripples through the entire call chain (DB queries, outbound HTTP calls, etc.) automatically, provided every function accepts and propagates `ctx`. This is what makes Go HTTP handlers resilient to client disconnects with zero extra code once the pattern is adopted.

## Zone of proximal development notes

Amit has now covered:
- Modules, project structure, HTTP handlers, routing, JSON, middleware, error handling, testing, project layout, goroutines — Lessons 1–11
- context.Context — this lesson

The HTTP server mission is now fully equipped. A natural **capstone** lesson (lesson 13) would be building a complete, well-structured mini HTTP server from scratch that uses all of these: `cmd/server/main.go`, `internal/handlers/`, `internal/middleware/`, JSON in/out, context propagation, error handling, and at least one test.

## What comes next

- **Capstone mini HTTP server** (Lesson 13): Write a complete project from scratch — routes, JSON, middleware, errors, context, tests. Mission goal in one lesson.
- **database/sql + pgx** (post-mission): Connecting the server to Postgres — context propagation makes this directly applicable.
