# Learning Record 0012 — Capstone: A Complete Go HTTP Server

## Date
2026-07-08

## Lesson
0013 — Capstone: A Complete Go HTTP Server

## What was taught

- **Full project layout**: `cmd/server/main.go` as wiring-only entrypoint; business logic in `internal/handlers`, `internal/middleware`, `internal/store`. Matches real Go project conventions from lesson 10.
- **sync.RWMutex**: Protecting a shared in-memory map from concurrent goroutines. `RLock()`/`RUnlock()` for reads (allows concurrency), `Lock()`/`Unlock()` for writes (exclusive). Correct choice when reads >> writes.
- **Handler struct pattern**: `type Handler struct { Store *store.Store }` — injects dependencies explicitly rather than using globals. Makes testing trivial: each test creates its own `store.New()`.
- **Centralised writeJSON / writeError helpers**: Avoid repeating `w.Header().Set("Content-Type", "application/json")` and `w.WriteHeader(status)` in every handler.
- **Go 1.22 r.PathValue("id")**: Extracting named path parameters from the built-in `ServeMux` without a third-party router.
- **Method-prefixed route patterns**: `"GET /tasks"`, `"POST /tasks"` — Go 1.22+ feature that replaces manual `r.Method` checks.
- **Graceful shutdown**: `signal.Notify` on `SIGINT`/`SIGTERM`, `srv.Shutdown(ctx)` with a timeout context. Prevents in-flight requests being cut off during deploys.
- **ListenAndServe in a goroutine**: Frees `main` to block on the signal channel instead.
- **httptest.NewRecorder()**: Unit-testing handlers without real HTTP connections — same pattern from lesson 8, now applied in the full project context.
- **go build -o**: Producing a single self-contained binary with no runtime dependency.

## TS → Go comparisons taught

| TypeScript (Express) | Go (stdlib) |
|---|---|
| `express()` + `app.use(logger())` | `mux := http.NewServeMux()` + `middleware.Logging(mux)` |
| `app.get('/tasks/:id', fn)` | `mux.HandleFunc("GET /tasks/{id}", h.GetTask)` |
| `req.params.id` (string) | `r.PathValue("id")` (string) |
| `process.on('SIGTERM', () => server.close(...))` | `signal.Notify(quit, syscall.SIGTERM); srv.Shutdown(ctx)` |
| No locking needed in Node (single-threaded event loop) | `sync.RWMutex` required for shared state in Go (every request = goroutine) |
| `jest` + `supertest` for handler tests | `net/http/httptest` — stdlib, no packages needed |

## Key insight

The project structure mirrors almost every real Go service: a thin `cmd/` entrypoint that wires together `internal/` packages, with each package having a single responsibility. The only difference between this capstone and a production service is persistence (database/sql + pgx) — the shape and the patterns are identical. Amit now has the full mental model to read, extend, and ship Go HTTP servers.

## Zone of proximal development notes

Amit has now completed the full HTTP server mission:
- Lessons 1–12: syntax, modules, handlers, routing, JSON, middleware, error handling, testing, project layout, goroutines, context
- Lesson 13: capstone — integrates all of the above into one runnable project

**Mission goal achieved.** Natural next topics:
- **database/sql + pgx**: Replace the in-memory store with Postgres. Context propagation from lesson 12 applies directly.
- **Authentication**: JWT middleware on top of the existing middleware chain.
- **Deployment**: Docker, fly.io, or a single `go build` binary on a VPS.
