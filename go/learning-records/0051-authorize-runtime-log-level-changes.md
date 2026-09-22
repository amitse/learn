# Learning Record 0051 — Authorize Runtime Log-Level Changes

## Date
2026-09-22

## Lesson
0051 — Authorize Runtime Log-Level Changes

## What was taught

- A runtime log-level change is a privileged state-changing operation, so authorization belongs in the application-owned `LogLevelControl`, not only in a particular HTTP route.
- Model a trusted caller explicitly as an application `Principal` with an ID and permission query, such as `Can("logs:write")`.
- Reject an unauthorized principal with a sentinel `ErrForbidden` before reading the level, acquiring the transition mutex, changing the `LevelVar`, or emitting an accepted-change audit event.
- Authentication adapters may construct the trusted `Principal`; raw headers, request-body actor fields, and arbitrary strings are not authority.
- Test denial through observable outcomes: `errors.Is(err, ErrForbidden)`, unchanged level, and no audit output. Keep the successful serialized-transition test from Lesson 50.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| authenticated principal with a permissions set | application `Principal` with `map[string]bool` and `Can` |
| `throw new ForbiddenError()` before state mutation | return sentinel `ErrForbidden` before transition work |
| service-level permission check | `LogLevelControl.Set(ctx, principal, to)` authorization guard |

## Key insight

A route can be replaced or bypassed; the operation that owns sensitive state cannot. Enforcing permission at the control boundary lets every future transport delegate to one safe rule, while an unauthorized attempt produces no operational side effect.

## Zone of proximal development notes

Amit already has a narrow runtime-level owner, independent audit logger, and mutex-protected transition. The smallest next step before exposing any administration transport is to make that owner require explicit, trusted authority and prove denial leaves both mutable policy and audit stream unchanged.

## What comes next

- Adapt the authenticated identity from the existing JWT middleware into the explicit `Principal` used by this control.
- Define an operations-only HTTP route only after choosing authentication, authorization, input validation, and audit-retention policy.
- Decide whether restart-time configuration is sufficient; do not expose runtime control merely because the code can support it.
