# Learning Record 0038 — Choose a Revision Precedence Rule

## Date
2026-08-27

## Lesson
0038 — Choose a Revision Precedence Rule

## What was taught

- A Go executable can expose revision metadata from an explicitly linker-stamped `revision` variable and from the optional `vcs.revision` entry in `debug.ReadBuildInfo().Settings`.
- The operational policy is deterministic: use a non-empty, non-fallback linker revision first; otherwise use a non-empty `vcs.revision`; otherwise return the honest local fallback `"none"`.
- `vcs.revision` is diagnostic fallback data, not a replacement for CI's explicit release identity, because build information may be unavailable or vary by build mode.
- Separating `selectedRevision(stamped, settings)` from `revisionForLog()` makes the precedence policy table-testable without relying on the runtime environment.
- Revision selection remains application-boundary work and is emitted once in lifecycle logging—not in request handlers or response bodies.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| CI-injected `GIT_SHA` wins over optional bundler metadata | linker-stamped `revision` wins over optional `vcs.revision` |
| inspect an optional metadata object defensively | guard `debug.ReadBuildInfo()` with `ok`, then inspect settings |
| pure function chooses config/metadata precedence | `selectedRevision(stamped, settings)` chooses identity precedence |

## Key insight

Multiple metadata sources are useful only after their authority is explicit. Prefer the CI-owned linker stamp, use Go's VCS setting as a safe fallback, and preserve `"none"` when neither source makes a claim.

## Zone of proximal development notes

Amit has linker-stamped version and revision logging plus a defensive `ReadBuildInfo` selector. The smallest next step is to test the selection rule with a table of stamped, fallback, and unavailable metadata states, then optionally move the established lifecycle fields into structured `log/slog` output.

## What comes next

- Write the table-driven selector test in the actual server package, including the empty-string edge case.
- Migrate one lifecycle log event to `log/slog` while retaining the same version/revision precedence policy.
- Keep build identity at the process boundary; do not expose it incidentally through HTTP handlers.
