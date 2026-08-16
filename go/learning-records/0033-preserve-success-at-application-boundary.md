# Learning Record 0033 — Preserve Success at the Application Boundary

## Date
2026-08-16

## Lesson
0033 — Preserve Success at the Application Boundary

## What was taught

- The application wrapper calls its Run-like dependency once whether the result is success or failure.
- `nil` is the normal Go representation of no `error`; when `Run` returns `nil`, the application boundary must return `nil` unchanged.
- `fmt.Errorf("run HTTP server: %w", err)` belongs only in the non-nil branch, where there is a failure to contextualize and preserve.
- A focused success-path test can inject `func() error`, record that it was called, return `nil`, and assert that the wrapper returns `nil`.
- This test protects the composition contract without opening a listener, starting a goroutine, sleeping, or testing `log.Fatal`.
- `main` owns process failure policy: it calls `log.Fatal` only if the returning boundary function produces a non-nil error; ordinary server completion simply returns from `main`.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| resolved `Promise<void>` | returned `nil` error |
| call an injected async function, then resolve normally | call an injected `func() error`, then return `nil` |
| `catch` creates contextual error only on rejection | `if err != nil` wraps only a non-nil error |
| process exits normally after awaited success | `main` returns normally after `nil` |

## Key insight

The error branch makes a failure more informative; the success branch should make nothing up. Returning `nil` unchanged preserves an honest lifecycle signal and keeps process policy at the edge.

## Zone of proximal development notes

Amit has just learned to wrap unexpected `Run` errors at the application boundary. The smallest complementary step is to test the normal result of that exact seam, reinforcing Go's `nil` error convention and complete two-outcome boundary reasoning without introducing new infrastructure.

## What comes next

- Add structured operational metadata (build version or environment) at the composition root while keeping it out of HTTP handlers.
- Decide what belongs in a production readiness signal versus normal shutdown logging.
- Consider an external supervisor's restart policy rather than building restart loops into `Run`.
