# Learning Record 0045 — Add Source Locations to slog Events

## Date
2026-09-10

## Lesson
0045 — Add Source Locations to `slog` Events

## What was taught

- `slog.HandlerOptions{AddSource: true}` tells an `slog` handler to attach source-location metadata to records it handles.
- Source inclusion is logger-construction policy, alongside the existing JSON representation and INFO minimum level; log call sites remain unchanged.
- In JSON output, source information is supporting metadata for investigation, not a substitute for a stable event message or named `version` and `revision` attributes.
- A focused test can use a `bytes.Buffer`, decode one JSON record into `map[string]any`, and require a non-empty `source` object.
- A source test should not assert a full serialized record, a particular file path, or a particular line number, because those details naturally change during refactoring.
- Enable source locations deliberately only where the extra diagnostic context helps the production log consumer.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| logger transport configured with caller metadata | `slog.HandlerOptions{AddSource: true}` |
| caller details added to every emitted event | handler attaches source metadata to every handled record |
| test event shape, not an exact source line | decode JSON and assert a populated `source` object |

## Key insight

Source location is optional diagnostic metadata, not event identity. Configure it once at the handler boundary and test that it is present without coupling your test to a code layout that will change.

## Zone of proximal development notes

Amit already has JSON lifecycle events with stable identity fields and an explicit minimum level, each verified with captured output. Adding source metadata is the narrow next handler option: it extends the same construction seam and JSON-test pattern without changing lifecycle control flow or introducing request logging.

## What comes next

- If request logs answer a concrete operational question, define one event with a stable message, named fields, and a deliberate level.
- If source locations are useful only in development, make the handler option an explicit configuration-boundary choice and test both policies.
- Validate the final JSON shape against the deployment log collector before treating it as an operational contract.
