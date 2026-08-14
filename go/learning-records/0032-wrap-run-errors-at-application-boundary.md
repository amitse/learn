# Learning Record 0032 — Wrap Run Errors at the Application Boundary

## Date
2026-08-14

## Lesson
0032 — Wrap Run Errors at the Application Boundary

## What was taught

- `Run` owns HTTP lifecycle mechanics: it normalizes only intentional `http.ErrServerClosed` and returns unexpected serving failures.
- The application edge owns process policy: it can add the operation name, log, and exit without placing that policy in `Run` or in handlers.
- `fmt.Errorf("run HTTP server: %w", err)` adds operational context while retaining the underlying error in Go's error chain.
- `errors.Is(wrapped, cause)` verifies that a wrapped sentinel remains discoverable; this is the stable assertion for the causal contract.
- Do not unit-test `log.Fatal`, because it calls `os.Exit(1)`. Extract a small returning function (`wrapRun` / `runApp`) and test its context-plus-cause behavior with an injected Run-like function.
- A useful boundary test asserts both independent outcomes: the returned text identifies the failed operation, and `errors.Is` recognizes the original failure.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `new Error("HTTP server stopped", { cause: err })` | `fmt.Errorf("run HTTP server: %w", err)` |
| top-level catch logs and sets exit code | `main` calls `log.Fatal(err)` after `runApp` returns |
| inspect `error.cause` | `errors.Is(err, sentinel)` walks the error chain |
| inject a rejecting function for a unit test | inject `func() error` for a deterministic boundary test |

## Key insight

An error has two audiences: an operator needs the failed operation, while code and tests need the original cause. Wrap once at the layer that knows the operation, with `%w`, so both audiences get what they need without making `Run` own process policy.

## Zone of proximal development notes

Amit has just proved that unexpected `Serve` failures reach `Run`. This lesson makes the smallest next composition step: preserve that failure while adding application-level context, using familiar `errors.Is` and a narrow function seam rather than introducing a logging framework.

## What comes next

- Thread structured operational metadata (request ID, version, environment) through an application logger without putting logging into handlers.
- Test the normal `nil` result passes through the same application boundary unchanged.
- Decide whether a supervisor/restart policy belongs outside the binary in the deployment environment.
