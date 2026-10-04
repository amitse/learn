# Learning Record 0057 — Test Private Error Logging

## Date
2026-10-04

## Lesson
0057 — Test Private Error Logging

## What was taught

- An unexpected application/control error has two intentional outputs: a generic JSON 500 response for the client and a structured server-side error event for the operator.
- The handler logs the original error as a named `slog` attribute, for example `logger.Error("log-level change failed", "error", err)`, before rendering the safe public error envelope.
- A focused handler test can inject `slog.NewJSONHandler(&bytes.Buffer{}, nil)`, make its existing control spy return a distinctive internal error, and exercise the route with `httptest.NewRecorder`.
- The test asserts the distinctive private text is absent from the HTTP body and present in captured logs, in addition to the expected 500 response.
- Decode the public JSON when asserting its API shape; use the distinctive string only to test diagnostic containment across the public/private boundary.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `logger.error({ err }, "change failed")` | `logger.Error("change failed", "error", err)` |
| injected test logger / captured transport | `slog.NewJSONHandler(&bytes.Buffer{}, nil)` |
| `expect(res.text()).not.toContain(privateValue)` | `!strings.Contains(w.Body.String(), private.Error())` |

## Key insight

A safe 500 response is only half an error boundary. The paired contract is deliberately asymmetric: clients receive a stable, non-sensitive message while operators retain the original cause in structured logs—and one focused test should prove both claims.

## Zone of proximal development notes

Amit already has a tested JSON error envelope and handler outcome table that proves internal details do not reach clients. Capturing a buffer-backed `slog` event is the smallest next step: it preserves the existing HTTP contract while testing that the lost client detail remains operationally available.

## What comes next

- Decode the captured JSON log event and assert the stable event message plus its `error` field, rather than relying only on a substring.
- Decide whether unexpected error events need request/correlation identifiers, then add them at the request boundary without exposing them as secrets.
- Apply the same public/private error contract to other handlers as the server grows.
