# Learning Record 0058 — Decode Private Error Log Events

## Date
2026-10-06

## Lesson
0058 — Decode Private Error Log Events

## What was taught

- A buffer-backed `slog.NewJSONHandler` test should decode a captured error record rather than only search its serialized bytes.
- A narrow decoder type can own just the stable `level`, `msg`, and `error` fields: `type logEvent struct { Level string; Msg string; Error string }` with JSON tags.
- `bytes.TrimSpace(logs.Bytes())` removes the newline emitted by `JSONHandler`, then `json.Unmarshal` converts the one record to testable data.
- Assert the error level, stable event message, and original private error field, while deliberately not asserting volatile `time`, source, or JSON key order.
- Retain Lesson 57's complementary response assertion: the private cause must be absent from the public JSON body.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `const event = JSON.parse(line)` | `json.Unmarshal(line, &event)` |
| `expect(event.msg).toBe("...")` | `if got.Msg != "..." { t.Errorf(...) }` |
| assert selected log object properties | decode a narrow `logEvent` struct and ignore dynamic fields |

## Key insight

A log assertion is strongest when it treats logging as an event contract, not formatted text: decode the structured record and verify the stable fields the route deliberately emits.

## Zone of proximal development notes

Amit already proved that a distinctive unexpected cause stays private to logs while clients receive a safe 500 envelope. The immediate refinement is to test the actual structured log schema—message, level, and named error field—without becoming brittle to timestamps or handler formatting.

## What comes next

- Add a request or correlation identifier at the request boundary and include it in unexpected-error events.
- Decide which identifier is safe to return to clients for support correlation, without exposing the private cause.
- Apply the decoded structured-event test pattern to other unexpected-error routes.
