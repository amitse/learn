# Learning Record 0010 — Goroutines & the HTTP Server's Concurrency Model

## Date
2026-06-29

## Lesson
0011 — Goroutines & the HTTP Server's Concurrency Model

## What was taught

- **Goroutine basics**: A goroutine is Go's lightweight unit of concurrent execution (~2 KB starting stack vs ~1 MB for an OS thread). Launched with `go someFunc()`. The Go runtime scheduler multiplexes goroutines onto OS threads across all available CPU cores — true parallelism, not an event loop.
- **`net/http` uses goroutines automatically**: `http.ListenAndServe` internally calls `go handleConn(conn)` for every incoming connection. Amit's handlers have always been running concurrently — this lesson made that explicit.
- **Data races**: Because handlers run concurrently, any package-level (or shared) variable written from a handler is a data race unless protected. Demonstrated with an unprotected `visitors++` counter.
- **`sync.Mutex`**: The standard fix — `mu.Lock()` / `mu.Unlock()` around shared writes. Brief mention of `sync/atomic` as an alternative for simple integer operations.
- **Race detector**: `go run -race` or `go test -race ./...` — instruments the binary to detect races at runtime. Zero-cost in production builds.
- **Background goroutines from handlers**: Pattern of responding immediately and launching `go func(u User) { doSlowWork(u) }(user)` for async side effects. Closure-capture bug explained and demonstrated.
- **Channels teaser**: Typed pipes for goroutine-to-goroutine communication. Not needed for the HTTP server mission, but named and placed in context.

## TS→Go comparisons taught

| TypeScript | Go |
|---|---|
| async/await on a single-threaded event loop | goroutines — truly parallel on multi-core |
| `someAsync().then(...)` | `go someFunc()` |
| `await result` | `result := <-ch` (channel receive) |
| shared state rarely a problem (single-thread) | shared state requires `sync.Mutex` or channels |
| OS thread via `new Worker(...)` | goroutine via `go func(){}()` |

## Key insight

Go's HTTP server is concurrent *by default* — no configuration, no thread pools to size. Every handler runs in a goroutine, and goroutines are cheap enough that the runtime handles thousands of concurrent requests comfortably. The only new responsibility this places on the developer: protect shared state. In TypeScript (Node.js), this is rare because the event loop serialises CPU work. In Go, it's always the developer's responsibility.

## Zone of proximal development notes

Amit now understands why concurrent HTTP servers in Go work the way they do. He has all the pieces to build and reason about a production-grade HTTP server:
- Modules, project layout, HTTP handlers, routing, JSON, middleware, error handling, testing — Lessons 1–10
- Concurrency model — this lesson

## What comes next

- **Capstone project** (Lesson 12, recommended): Build one well-structured mini HTTP server from scratch — `cmd/server/main.go`, `internal/handlers/`, `internal/middleware/`, JSON in/out, proper errors, tests. The mission goal in one lesson.
- **Database layer** (out of mission scope for now): `database/sql` + `lib/pq` or `pgx`, connecting the server to Postgres.
- **`context.Context`** (mission-adjacent): How HTTP requests carry timeouts and cancellation signals — essential for production, naturally follows goroutines.
