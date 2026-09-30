# Learning Record 0055 — Write a Consistent JSON Error Envelope

## Date
2026-09-30

## Lesson
0055 — Write a Consistent JSON Error Envelope

## What was taught

- A small public error type, `errorResponse{Error string \`json:"error"\`}`, creates one predictable JSON body shape for client-visible failures.
- `writeJSONError` owns rendering only: set `Content-Type: application/json`, call `WriteHeader(status)`, then encode the chosen public message.
- The handler still owns classification: missing identity maps to 401, `ErrForbidden` maps to 403, and unexpected control failures map to a generic 500 while their details go to structured logs.
- Headers must be set before `WriteHeader`; a 204 success remains a separate no-body response rather than using the JSON error helper.
- A focused `httptest.NewRecorder` test can assert status, media type, and decoded message without coupling to formatting details.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `res.status(status).json({ error: message })` | set header, `WriteHeader`, then `json.NewEncoder(w).Encode(errorResponse{...})` |
| Express error renderer | explicit transport helper `writeJSONError` |
| error middleware chooses response | handler classifies expected vs unexpected errors before rendering |

## Key insight

A JSON error envelope is a public API contract, not a serialization of Go errors. Centralize its rendering for consistency, but keep status selection and server-side diagnostics at the handler/application boundary.

## Zone of proximal development notes

Amit has just added a safe generic 500 branch with a tested empty-body contract. Defining one minimal JSON error body is the next small transport improvement: it preserves the no-leak rule while making all client error bodies predictable.

## What comes next

- Add a handler-level table test that verifies every error outcome has the selected JSON envelope and that 500 omits the internal cause.
- Decide whether clients need stable machine-readable error codes in addition to the short public message.
- If the operations surface grows, split request parsing, principal adaptation, and response rendering into focused transport helpers.
