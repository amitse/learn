# Learning Record 0031 — Test an Unexpected Serve Failure

## Date
2026-08-13

## Lesson
0031 — Test an Unexpected Serve Failure

## What was taught

- `Run` has two distinct terminal outcomes: intentional shutdown is normalized from `http.ErrServerClosed` to `nil`, while an unexpected `Serve` failure must remain an error.
- A narrow `net.Listener` fake can return a chosen error from `Accept`, making an unexpected server failure deterministic without binding a port or relying on time.
- The fake needs only `Accept`, `Close`, and `Addr`; it is a small interface seam, not a fake HTTP server.
- `errors.Is(err, errAccept)` asserts error identity through a possible wrapping chain and avoids brittle text comparison.
- This test complements the existing cancellation, admission, drain, deadline, and resource-cleanup tests: it covers the non-shutdown branch of the lifecycle owner.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| Propagate a failed `server.listen()` rather than treating it as `close` | Return an `Accept`/`Serve` error rather than treating it as `http.ErrServerClosed` |
| Minimal test double that rejects startup | `net.Listener` fake whose `Accept` returns a sentinel error |
| Check a rejected error's identity/cause | `errors.Is(err, errAccept)` |

## Key insight

A lifecycle wrapper should translate only the expected termination signal it owns. If it turns every serving failure into success, the application loses its ability to distinguish a clean stop from an infrastructure failure.

## Zone of proximal development notes

Amit already has a testable `Run` boundary and deterministic tests for its intended cancellation path. The smallest useful next step is the complementary failure-path test, using the same standard-library interface and established `errors.Is` practice, without adding another shutdown scenario.

## What comes next

- Decide the application-edge policy for `Run` errors (log and exit or report to a supervisor) while keeping policy out of `Run`.
- Write a focused test for the chosen policy at the composition root.
- Optionally wrap unexpected serve errors with operational context while retaining `errors.Is` behavior.
