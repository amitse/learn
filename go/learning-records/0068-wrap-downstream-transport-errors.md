# Learning Record 0068 — Wrap Downstream Transport Errors

## Date
2026-10-26

## Lesson
0068 — Wrap Downstream Transport Errors

## What was taught

- An outbound HTTP adapter should add stable operation context when `http.Client.Do` fails, such as `get profile`.
- `fmt.Errorf("get profile: %w", err)` preserves the original error in Go's error chain; `%v` only formats text and does not wrap.
- Callers use `errors.Is` to recognize a wrapped sentinel or context error such as `context.Canceled` or `context.DeadlineExceeded` without parsing an error message.
- A test can inject a minimal failing `http.RoundTripper`, then require both the operation label and the preserved cause—without contacting a network.
- This adapter returns an application error; the HTTP route remains responsible for mapping it to the safe public error envelope.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `new Error("get profile", { cause: err })` | `fmt.Errorf("get profile: %w", err)` |
| inspect `error.cause` | `errors.Is(err, cause)` |
| stubbed `fetch` rejection | injected `http.RoundTripper` returning a selected error |

## Key insight

A wrapped error carries two different things at once: a stable description of the failed operation for humans and the original cause for program decisions. Preserve both at the dependency boundary instead of converting a transport failure into unstructured text.

## Zone of proximal development notes

Amit already creates an outbound request with the selected request ID and has used `%w` plus `errors.Is` at the application boundary. This lesson applies that established error-chain pattern to the immediately adjacent dependency boundary, using one small fake transport rather than adding retries, a client framework, or distributed tracing.

## What comes next

- Test that cancellation of an inbound request context interrupts a deliberately blocked downstream request.
- Decide a narrow policy for retryable dependency failures separately from error wrapping.
- Map a wrapped dependency failure to the existing safe JSON 500 contract while retaining its private cause in structured logs.
