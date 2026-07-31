# Learning Record 0025 — Make Run Own the Server Lifecycle

## Date
2026-07-31

## Lesson
0025 — Make Run Own the Server Lifecycle

## What was taught

- Server startup and intentional shutdown form one lifecycle boundary; a `Run` function should own the `Serve` call and its expected terminal error.
- `http.Server.Serve(ln)` blocks and returns `http.ErrServerClosed` after `Shutdown` or `Close`; this is a normal lifecycle result, not an operational failure.
- `errors.Is(err, http.ErrServerClosed)` recognizes the expected sentinel while retaining correct behavior if an error is wrapped.
- Start `Run` in a goroutine when another actor must initiate `Shutdown`; use a buffered `chan error` to carry its one final result without timing coupling.
- An ephemeral `net.Listener` makes the lifecycle test real but independent of a fixed port.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `listen` then wait for `close` | `Serve` then interpret `ErrServerClosed` |
| server wrapper owns start/stop semantics | `Run(srv, ln)` owns serving lifecycle semantics |
| async task reports one completion result | `done := make(chan error, 1)` receives Run's terminal result |

## Key insight

`Serve` returning is not automatically a failure. Lifecycle ownership gives the application one precise place to translate an intentional server stop into success while preserving genuinely unexpected serving errors.

## Zone of proximal development notes

Amit has already tested graceful shutdown's drain, admission, and deadline contracts using real listeners and deterministic channels. This lesson packages that knowledge into a small application boundary and one focused lifecycle test, without introducing new infrastructure.

## What comes next

- Give `Run` a cancellation/signal input so production `main` can request shutdown without containing lifecycle logic.
- Extend the same owner to a `pgxpool.Pool`, closing it only after HTTP shutdown completes.
- Add a test for a non-`ErrServerClosed` `Serve` failure using a narrow server/listener seam.
