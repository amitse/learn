# Learning Record 0020 — Server Wiring with Ephemeral Listeners

## Date
2026-07-24

## Lesson
0021 — Wire a Server to an Ephemeral Listener

## What was taught

- `http.ListenAndServe` combines binding and serving, while `net.Listen` returns the concrete `net.Listener` needed by a wiring test.
- Binding `127.0.0.1:0` delegates port selection to the OS; after success, `ln.Addr().String()` contains the actual address and port.
- `http.Server{Addr, Handler}` separates server construction from the network side effect; a small `NewServer` factory is easy to inspect and test.
- `srv.Serve(ln)` blocks, so tests start it in a goroutine before making a local HTTP request.
- `t.Cleanup(func() { _ = srv.Close() })` guarantees listener cleanup even after `t.Fatal`.
- `http.ErrServerClosed` is the expected return from `Serve` after `Close` or `Shutdown`; other errors should fail the test.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `app.listen(0)` then inspect `server.address().port` | `net.Listen("tcp", "127.0.0.1:0")` then inspect `ln.Addr()` |
| `server.listen(...)` returns a server handle | `net.Listen(...)` returns the listener handle |
| `server.close()` in teardown | `t.Cleanup(func() { _ = srv.Close() })` |
| start server then fetch its assigned port | `go srv.Serve(ln)` then request `http://` + `ln.Addr()` |

## Key insight

The requested configuration address and the bound address are different values when the port is `0`: `srv.Addr` is the request, while `ln.Addr()` is the fact. A production-like wiring test should use the listener’s fact, giving every test a collision-free port without guessing one.

## Zone of proximal development notes

Amit already has validated configuration, including the intentional `PORT=0` boundary, plus HTTP handler and graceful-shutdown foundations. This lesson connects those pieces into one small real-network test without introducing external dependencies or a hard-coded port.

## What comes next

- Replace `srv.Close()` in the test cleanup with a bounded `srv.Shutdown(ctx)` and test that an in-flight request is allowed to finish.
- Or return to persistence: use `pgxpool` for native Postgres pooling, then apply the same explicit startup/shutdown wiring.
