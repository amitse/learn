# Learning Record 0049 — Audit Runtime Log-Level Changes

## Date
2026-09-18

## Lesson
0049 — Audit Runtime Log-Level Changes

## What was taught

- A live log-level change is an operational action and belongs in one application-owned control object, rather than at arbitrary HTTP handlers or direct `LevelVar.Set` call sites.
- Read the prior threshold with `LevelVar.Level()`, update the shared variable with `Set`, then emit a stable structured `log level changed` event containing `actor`, `from`, and `to`.
- The change audit must use a logger whose filtering policy is independent of the mutable application logger. Otherwise a transition can suppress the event reporting itself.
- A focused test captures JSON, decodes the audit record, and asserts the event name, actor, and transition—not a timestamp or complete serialized line.
- `LevelVar` supports concurrent reads and updates, but a strict ordered history of simultaneous changes needs an explicit serialization and a sink that can preserve that ordering.
- This lesson deliberately does not create an administrative endpoint: authenticating callers, authorizing them, parsing inputs, and choosing audit retention are separate decisions.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| dedicated audit logger with fixed policy | separate `*slog.Logger` for audit events |
| `level.current` before update | `level.Level()` |
| central `setLevel(actor, to)` service method | application-owned `LogLevelControl.Set(ctx, actor, to)` |
| structured audit properties | `InfoContext(ctx, "log level changed", "actor", actor, "from", from, "to", to)` |

## Key insight

A mutable log threshold is not sufficient observability by itself: the change needs an independently observable, structured record. One explicit control boundary makes the action reviewable without granting application request handlers operational power.

## Zone of proximal development notes

Amit has already configured a typed startup threshold and shared one `LevelVar`. The smallest next step is to make that operational capability accountable through a narrow owner and an output-based test, while keeping transport authentication and authorization out of scope until the audit contract is clear.

## What comes next

- Define an authenticated and authorized operations transport only if runtime verbosity is genuinely needed.
- Decide with the deployment team where audit events go and how long they must be retained.
- Add a concrete request-log event only after naming an operator question and its stable fields.
