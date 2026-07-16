# Learning Record 0016 — Graceful Shutdown

## Date
2026-07-16

## Lesson
0017 — Graceful Shutdown

## What was taught

- **Why graceful shutdown matters**: SIGTERM is sent before forceful kill; dropping the listener without draining requests causes broken responses in production.
- **`http.Server` as a value**: creating `srv := &http.Server{Addr: ":8080", Handler: mux}` rather than calling `http.ListenAndServe` directly enables calling `srv.Shutdown` later.
- **Goroutine for ListenAndServe**: must be in a goroutine so `main` keeps running to reach signal-handling code.
- **`http.ErrServerClosed`**: expected sentinel returned by `ListenAndServe` when `Shutdown` is called — should be filtered with `errors.Is`, not treated as a crash.
- **`signal.Notify`**: bridges OS signals into a buffered Go channel; buffer size 1 avoids signal loss if goroutine is momentarily not reading.
- **`<-quit` blocks main**: idiomatic Go pattern equivalent to awaiting an event in TypeScript.
- **`context.WithTimeout` + `defer cancel()`**: gives handlers a deadline to finish; `defer cancel()` releases the internal timer goroutine even if Shutdown returns before the deadline.
- **Post-shutdown cleanup ordering**: resources (DB connections etc.) should be closed *after* `Shutdown` returns — at that point no handlers are still running.
- **Limitation**: `Shutdown` does not interrupt hijacked connections (WebSocket, HTTP/2 server push).

## TS → Go comparisons taught

| TypeScript (Fastify/Node) | Go (net/http) |
|---|---|
| `process.on('SIGTERM', async () => { await server.close() })` | `signal.Notify(quit, syscall.SIGTERM); <-quit; srv.Shutdown(ctx)` |
| Promise-based close | Channel-based blocking |
| Inline async callback | Explicit context deadline |

## Key insight

Go's shutdown pattern is a direct composition of three orthogonal primitives (channels, contexts, `Shutdown`) rather than a single framework method. Once you see each piece separately, the whole pattern feels obvious.

## Zone of proximal development notes

Amit now has a production-ready server skeleton (HTTP, JSON, middleware, JWT, graceful shutdown). Natural next steps:
- **pgxpool** — replace `database/sql` with native pgx pool (lower overhead, richer error types)
- **testcontainers-go** — integration-test DB handlers with ephemeral real Postgres
- **Config management** — reading ENV / config files (`os.Getenv`, `github.com/caarlos0/env`)
