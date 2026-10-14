# Learning Record 0062 — Log Request-ID Generator Failures Privately

## Date
2026-10-14

## Lesson
0062 — Log Request-ID Generator Failures Privately

## What was taught

- A failed request-ID generator has two deliberately separate outputs: a generic JSON 500 for the client and one private structured ERROR event for operators.
- Inject `*slog.Logger` into the middleware so log destination and representation are application-boundary choices and the middleware remains testable.
- Use a stable event message (`"request ID generation failed"`) with the changing cause in a named `"error"` attribute.
- A buffer-backed `slog.NewJSONHandler` lets a test decode just the owned `level`, `msg`, and `error` fields rather than asserting timestamp, JSON order, or formatter internals.
- The log event must not claim a `request_id`: generation failed before an ID existed. The test should still require that the private error is absent from the HTTP response.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `logger.error({ err }, "request ID generation failed")` | `logger.Error("request ID generation failed", "error", err)` |
| captured logger transport + `JSON.parse` | `bytes.Buffer` + `slog.NewJSONHandler` + `json.Unmarshal` |
| safe `res.status(500).json(...)` kept separate from logs | `writeJSONError(...)` kept separate from `logger.Error(...)` |

## Key insight

Observability is not permission to disclose. A useful failure event retains the real cause for operators, while the HTTP boundary remains intentionally uninformative to clients; each output needs its own focused assertion.

## Zone of proximal development notes

Amit already knows buffer-backed slog tests, decoded log-event assertions, safe JSON errors, and the deterministic generator-failure seam. This lesson composes those existing skills around one narrow failure boundary without introducing a new logging framework or changing the public error contract.

## What comes next

- Add a low-cardinality request-ID generation failure metric, if the deployment has a metrics boundary; never use `err.Error()` as a label.
- Define an explicit trust policy before accepting an upstream correlation ID from a trusted proxy.
- If upstream IDs are permitted, validate and normalize them before setting the response header or request context.
