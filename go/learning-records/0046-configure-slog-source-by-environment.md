# Learning Record 0046 — Configure slog Source by Environment

## Date
2026-09-11

## Lesson
0046 — Configure `slog` Source by Environment

## What was taught

- Source-location inclusion is deployment policy: carry it as a typed `Config.LogSource` boolean into logger construction, where it becomes `slog.HandlerOptions{AddSource: cfg.LogSource}`.
- Raw `LOG_SOURCE` text belongs at the configuration boundary, not inside `newLogger`, an HTTP handler, or a log call.
- `strconv.ParseBool` provides strict boolean parsing; an absent or empty value can intentionally default to `false`, while an invalid value should return a contextual configuration error rather than silently changing observability.
- The existing injected `Lookup` function makes parsing deterministic: table-test missing, enabled, disabled, and invalid states without mutating process environment.
- A separate buffer-backed JSON logger test can assert both policies: source is absent when disabled and present when enabled. It must not depend on a particular file path or line number.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| typed `config.logSource` passed to logger factory | `Config.LogSource` passed to `newLogger` |
| environment validation at bootstrap | injected `Lookup` + `strconv.ParseBool` at config loading |
| logger option `includeCaller: config.logSource` | `HandlerOptions{AddSource: cfg.LogSource}` |

## Key insight

A source location is optional handler metadata, and choosing it is configuration policy. Convert environment input to a typed value once, then let logger construction apply that value consistently to every emitted event.

## Zone of proximal development notes

Amit already knows the configuration boundary, injected lookup seam, JSON `slog` output, explicit minimum level, and source-presence testing. This lesson connects those existing pieces through one boolean without introducing request logging or changing lifecycle behavior.

## What comes next

- Define one request-log event only after naming the concrete operational question it answers; keep it separate from lifecycle events.
- If a deployment needs runtime-adjustable log levels, parse a level setting at the same configuration boundary and test its accepted values.
- Validate source and JSON policy with the actual deployment log collector.
