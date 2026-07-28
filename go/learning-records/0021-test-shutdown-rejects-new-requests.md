# Learning Record 0021 — Shutdown Rejects New Requests

## Date
2026-07-28

## Lesson
0023 — Test Shutdown Rejects New Requests

## What was taught

- `http.Server.Shutdown(ctx)` closes open listeners before waiting for active connections to become idle.
- A graceful-shutdown test has two independent assertions: an in-flight handler may finish, while a request requiring a new TCP connection is rejected.
- A small `net.Listener` wrapper can override `Close` and close a channel, making listener shutdown observable without a scheduling-based `time.Sleep`.
- `sync.Once` makes that close signal safe if test cleanup invokes `Close` again.
- A client with `http.Transport{DisableKeepAlives: true}` forces the assertion to use a new connection rather than a reused keep-alive connection.
- After confirming listener closure, a failed client `Get` is expected; after releasing the in-flight handler, `Shutdown` should return `nil`.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `server.close()` stops accepting new connections | `srv.Shutdown(ctx)` closes listeners first |
| wrap or spy on `server.close` to observe lifecycle | wrap `net.Listener.Close` and signal a channel |
| a fresh `fetch`/agent connection tests admission | `Transport{DisableKeepAlives: true}` forces a new connection |
| existing response can finish during close | active handler drains before `Shutdown` returns |

## Key insight

“Shutdown started” is not a sufficiently precise test event. The listener’s `Close` call is the meaningful boundary: only after it has occurred can a test reliably assert that a new connection cannot enter the server.

## Zone of proximal development notes

Amit can already wire an ephemeral listener and deterministically prove that shutdown drains an active request. This lesson extends that same channel-based coordination to the complementary admission boundary, keeping the test real-network and free of timing guesses.

## What comes next

- Test the deadline path: when an active request exceeds the shutdown context, `Shutdown` returns `context.DeadlineExceeded`.
- Extract server startup and shutdown into an application `Run` function with explicit lifecycle ownership.
- Return to persistence by wiring a database pool into that same lifecycle.
