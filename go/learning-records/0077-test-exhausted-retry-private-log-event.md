# Learning Record 0077 — Test the Exhausted-Retry Private Log Event

## Date
2026-11-13

## Lesson
0077 — Test the Exhausted-Retry Private Log Event

## What was taught

- The exhausted dependency-retry branch has two complementary observable contracts: a stable public 502 response and one private structured failure event.
- A test can use `bytes.Buffer` with `slog.NewJSONHandler`, then decode one JSON record into a deliberately narrow struct.
- The handler-owned event fields are the stable message, selected `request_id`, and named error attribute; timestamps, source metadata, and JSON key order are not owned by this test.
- A known request ID placed in request context proves the failure event uses the inbound-selected correlation value rather than raw request input or a new ID.
- The distinctive dependency cause belongs in the decoded private event but must remain absent from the HTTP response body.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| capture a structured logger mock in an array | capture `slog.NewJSONHandler(&buffer, nil)` output |
| inspect selected event properties | `json.Unmarshal(bytes.TrimSpace(buffer.Bytes()), &event)` |
| `expect(event).toMatchObject(...)` | assert narrow decoded owned fields |

## Key insight

A safe error boundary should not make failures opaque. Test its two outputs independently: the client receives an intentionally small stable contract, while operators receive the correlation ID and private cause needed to investigate.

## Zone of proximal development notes

Amit already has buffer-backed and decoded `slog` testing from Lessons 57–58, request-ID propagation from Lessons 59–67, and the safe exhausted-retry 502 boundary from Lesson 76. This lesson composes those established seams into one precise regression test without changing retry eligibility, retry count, or response mapping.

## What comes next

- Decide whether a narrowly defined upstream HTTP response status is eligible for the one approved retry.
- Table-test that response-status classifier separately from transport and context errors.
