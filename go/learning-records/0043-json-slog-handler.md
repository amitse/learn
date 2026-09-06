# Learning Record 0043 — Emit Lifecycle Logs as JSON

## Date
2026-09-06

## Lesson
0043 — Emit Lifecycle Logs as JSON

## What was taught

- `slog.NewJSONHandler(os.Stderr, nil)` selects JSON rendering for structured log records at the application boundary.
- Changing from `TextHandler` to `JSONHandler` does not change the lifecycle event contract: its stable message, level, and named `version`/`revision` attributes remain the same.
- `JSONHandler` writes newline-delimited complete JSON objects; this makes stderr output suitable for a log collector that parses JSON.
- A focused test can create `slog.NewJSONHandler(&bytes.Buffer{}, nil)`, decode the captured object with `json.Unmarshal`, and assert the message, level, and identity fields.
- Tests should not compare an exact timestamp or entire serialized log line, because the handler adds time and formatting is not the lifecycle contract.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| choose a JSON transport/formatter for `logger.info({ version, revision }, msg)` | construct `slog.New(slog.NewJSONHandler(os.Stderr, nil))` and keep `logger.Info(msg, slog.String(...))` |
| parse captured JSON and inspect object properties | `json.Unmarshal(out.Bytes(), &got)` then inspect `got["msg"]`, `got["level"]`, etc. |

## Key insight

Structured event data and its rendering are separate concerns. Keep stable lifecycle messages and attributes at the application boundary; select text or JSON once through the injected `slog.Handler` according to the deployment’s log consumer.

## Zone of proximal development notes

Amit already has structured lifecycle identity at startup, failure, and clean completion, plus buffer-backed `slog` tests. This lesson makes the existing events machine-readable without introducing request logging, new lifecycle behavior, or an external logging dependency.

## What comes next

- Add a narrow request-log event only if operators have a specific per-request question, keeping it separate from lifecycle events.
- Configure `slog.HandlerOptions` deliberately when source locations or a minimum log level are operational requirements.
- Validate the selected output format with the actual deployment log collector.
