# Learning Record 0026 — Let Run React to Cancellation

## Date
2026-08-03

## Lesson
0026 — Let Run React to Cancellation

## What was taught

- A lifecycle owner needs a cancellation input that means **begin shutdown**, not an OS-signal dependency.
- `signal.NotifyContext(context.Background(), syscall.SIGINT, syscall.SIGTERM)` belongs in `main`; it turns process signals into a cancellable context. Its returned stop function must be deferred to restore normal signal behaviour.
- `Run(stop context.Context, srv, ln, grace)` can wait with `select` for either `srv.Serve` to finish or `stop.Done()` to close.
- `srv.Serve` remains in a goroutine and reports one terminal result through a buffered `chan error`, as established in Lesson 25.
- The stop context is already cancelled when the shutdown branch runs. It must **not** be passed to `srv.Shutdown` as its drain budget.
- Create a fresh `context.WithTimeout(context.Background(), grace)` for `Shutdown`; this separates the stop trigger from the bounded grace period for active requests.
- After a successful `Shutdown`, `Run` receives the serve result and normalizes expected `http.ErrServerClosed` with `errors.Is`.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `AbortController` / a SIGTERM handler | `signal.NotifyContext` |
| `signal.abort` means begin server close | `stop.Done()` means begin `Shutdown` |
| independent close grace period | fresh `context.WithTimeout(..., grace)` |
| pass `AbortSignal` to a testable shutdown function | pass `context.Context` to `Run` |

## Key insight

“Stop now” and “how long may existing work drain?” are different policies. A cancelled stop context is the correct event to *start* graceful shutdown but the wrong deadline to *run* it with. Keeping them separate makes shutdown behavior both correct and explicit.

## Zone of proximal development notes

Amit already understands context cancellation, graceful shutdown, deterministic lifecycle tests, and `Run` as the lifecycle owner. This lesson combines those concepts into a production-ready boundary while preserving testability: tests can cancel a context without sending an OS signal.

## What comes next

- Write a deterministic test: cancel `Run` while one request is active, release the request, and assert `Run` returns nil.
- Extend `Run` to close a `pgxpool.Pool` only after HTTP shutdown has drained.
- Add a narrow seam to test an unexpected `Serve` failure separately from intentional shutdown.
