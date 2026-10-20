# Learning Record 0065 — Test Selected Request-ID Propagation

## Date
2026-10-20

## Lesson
0065 — Test Selected Request-ID Propagation

## What was taught

- A selector unit test establishes which ID should win; a focused middleware test establishes that the one selected ID reaches every output the middleware owns.
- For a trusted but malformed upstream `X-Request-ID`, inject a deterministic generated fallback, run a failing downstream handler, and assert that both the response `X-Request-ID` header and decoded private log event carry that fallback.
- A deliberately failing handler activates the private logging path while the response-header assertion checks the public correlation path; the public error body remains generic.
- Capture structured logs with a buffer-backed `slog.NewJSONHandler` and decode only the owned `request_id` field. Avoid assertions on timestamps, JSON order, or formatter internals.
- Include a narrow no-leak check that the distinctive invalid upstream value does not occur in the response body. An invalid value must not reappear in a downstream event as an alternate correlation key.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| Supertest response plus parsed captured event | `httptest.NewRecorder` plus `json.Unmarshal` of a buffer-backed `slog` event |
| fixed `requestId()` test double | injected `func() (string, error)` generator returning a known fallback |
| `expect(event.request_id).toBe(id)` | decode a narrow event struct and compare `RequestID` |

## Key insight

A request ID is a cross-output correlation contract, not merely a header implementation detail. Once selection rejects raw input, every owned output must use the same selected ID; otherwise operators and clients can no longer correlate the same request safely.

## Zone of proximal development notes

Amit already has a generated fallback, trust gate, allowlist validator, safe JSON errors, and decoded structured-log assertions. This lesson composes those known seams into one narrow end-to-end boundary test without introducing tracing infrastructure or a new logging framework.

## What comes next

- Add a compact table of propagation cases for trusted-valid, trusted-invalid, untrusted-valid, and missing upstream IDs.
- If a standard trace context is needed, model its extraction and propagation as a separate adapter with its own grammar and trust rules.
- Consider a low-cardinality rejection metric only at a metrics boundary; never attach raw rejected IDs as labels.
