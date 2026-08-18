# Learning Record 0034 — Log Lifecycle Events at the Application Boundary

## Date
2026-08-18

## Lesson
0034 — Log Lifecycle Events at the Application Boundary

## What was taught

- The application boundary is the smallest layer that can truthfully label the overall HTTP lifecycle as a clean stop or a failure.
- A successful `Run` result remains `nil`; only after that return may the boundary emit a normal completion message such as `HTTP server stopped`.
- A returning `runApp(run func() error, logger *log.Logger) error` keeps lifecycle behavior testable while `main` retains the policy to print a failure and exit non-zero.
- `log.New(&buffer, "", 0)` creates an isolated logger whose output can be inspected in a test without mutating global logging or starting a server.
- The focused success test supplies a `func() error` that returns `nil`, asserts the returned `nil`, and checks that the buffer contains the lifecycle event.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `logger.info("server stopped")` after `await runServer()` | `logger.Print("HTTP server stopped")` after `Run` returns `nil` |
| injected logger writing to an in-memory sink | `log.New(&bytes.Buffer{}, "", 0)` |
| top-level code sets an exit code after a caught error | `main` calls `os.Exit(1)` only after `runApp` returns an error |

## Key insight

A lifecycle log is operational policy, not HTTP mechanics. Put it beside the process decision: log a clean stop only after the lifecycle owner completes, and let failures return to the outermost edge for the exit decision.

## Zone of proximal development notes

Amit has a testable application wrapper that preserves both the failure cause and a `nil` success result. The next small step is making a normal completion observable with the standard logger and a buffer—using the same narrow `func() error` seam, with no new networking or concurrency concepts.

## What comes next

- Add build version metadata to the boundary-owned lifecycle messages.
- Decide which static process metadata belongs in logs versus a health endpoint.
- Consider replacing plain text with `log/slog` structured fields once the two-event contract is stable.
