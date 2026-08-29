# Learning Record 0039 — Test a Revision Precedence Table

## Date
2026-08-29

## Lesson
0039 — Test a Revision Precedence Table

## What was taught

- A table-driven test is the appropriate executable specification for the pure `selectedRevision` policy established in Lesson 38.
- The test contains three essential outcomes: a non-fallback linker stamp wins over `vcs.revision`; a non-empty `vcs.revision` is used when the stamp is `"none"`; and a blank `vcs.revision` still yields the honest `"none"` fallback.
- Keep this test focused on the pure selector rather than `debug.ReadBuildInfo`; runtime build-info availability is toolchain behavior, while precedence is application policy.
- `t.Run(tt.name, ...)` gives each policy row a useful failure name, and a single `t.Fatalf` makes a wrong selected revision immediately clear.
- The test belongs in the same package as the selector, allowing the selector to remain unexported application-boundary code.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `test.each` input/output matrix | slice of case structs plus a `for` loop |
| named test callback | `t.Run(tt.name, func(t *testing.T) { ... })` |
| one expectation per configuration row | one `got != want` assertion per subtest |

## Key insight

Precedence is a deploy contract, not an implementation detail. A small table turns “stamped → VCS → none” into executable evidence and prevents an empty optional build setting from silently becoming a false identity.

## Zone of proximal development notes

Amit can now choose revision metadata defensively and already knows Go table-driven tests. The next small extension is to preserve the same lifecycle identity fields while changing only their presentation from formatted text to structured `log/slog` attributes.

## What comes next

- Migrate one startup lifecycle event to `log/slog` with explicit `version` and `revision` fields.
- Test structured lifecycle output through an injected `slog.Handler` or buffer-backed handler.
- Keep identity selection and logging at the application boundary; do not introduce it into HTTP handlers.
