# Learning Record 0076 — Map an Exhausted Retry to 502

## Date
2026-11-10

## Lesson
0076 — Map an Exhausted Retry to 502

## What was taught

- When this HTTP route cannot obtain a profile after its one approved retry, it reports an upstream dependency failure with `http.StatusBadGateway` (502), not a generic 500.
- The client receives the existing stable JSON envelope, here `{"error":"profile service unavailable"}`; it must never receive `err.Error()`.
- The wrapped dependency error is retained as a private structured-log attribute via `slog.Any("error", err)`, alongside a stable event message and request ID.
- This mapping applies to the exhausted approved dependency-retry branch only; cancellation and deadline exhaustion retain their separate semantics and are not rewritten as upstream failure.
- A handler test can inject a distinctive wrapped dependency failure, then assert 502, decoded JSON, and no leakage without a network or retry delay.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `res.status(502).json({ error: "profile service unavailable" })` | `writeJSONError(w, http.StatusBadGateway, "profile service unavailable")` |
| `logger.error({ err }, "profile dependency failed")` | `logger.Error("profile dependency failed", slog.Any("error", err))` |
| safe public error while preserving private cause | stable JSON envelope plus wrapped error in structured logs |

## Key insight

A retry policy decides whether one more dependency call is allowed; an HTTP boundary decides what the client can safely learn when that policy is exhausted. Keep the public gateway contract stable and the real cause private.

## Zone of proximal development notes

Amit already has a consistent JSON error envelope and decoded-private-log testing (55–58), selected request-ID propagation (59–67), and a bounded cancellable retry policy (71–75). This lesson connects those established pieces at one precise boundary without changing retry count, delay, or cancellation policy.

## What comes next

- Test the exhausted-retry private log event by decoding its owned fields, including the selected request ID.
- Decide whether a narrowly defined upstream response status should be retryable before the first retry is attempted.
