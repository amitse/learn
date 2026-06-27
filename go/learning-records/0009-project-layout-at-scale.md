# Learning Record 0009 — Project Layout at Scale

## Date
2026-06-27

## Lesson
0010 — Project Layout at Scale

## What was taught

- **Package = directory**: Every `.go` file in the same directory belongs to the same package and shares access to unexported identifiers — no import needed. This is the fundamental encapsulation unit in Go.
- **`internal/`**: A specially-enforced directory. The Go compiler refuses to allow any code *outside* the module (rooted at the parent of `internal/`) to import from it. This is Go's mechanism for package-level privacy.
- **Standard layout**: `cmd/server/main.go` as the thin binary entry point; `internal/handlers/`, `internal/middleware/`, `internal/apperrors/` as the private sub-packages.
- **Thin `main.go` pattern**: The entry point wires dependencies and starts the server. Zero business logic lives there. All logic lives in `internal/` packages.
- **Full module path imports**: Sub-packages are imported using the full module path declared in `go.mod` — e.g. `"github.com/amit/myserver/internal/handlers"`. Relative imports (`./`) are not supported.
- **Package naming rules**: Lowercase, one word, matches the directory name. Avoid `util`, `common`, `misc` — they become dumping grounds with meaningless call-sites.
- **Multiple binaries**: `cmd/` can hold multiple entry points (e.g. `cmd/server/`, `cmd/migrate/`), each building into a separate binary with `go build ./cmd/server`.

## TS→Go comparisons taught

| TypeScript | Go |
|---|---|
| `import { fn } from './handlers/users'` | `import "myserver/internal/handlers"` then `handlers.Fn()` |
| `export class AppError` in `errors/AppError.ts` | `type AppError struct` in `internal/apperrors/errors.go` |
| `import '@/handlers/users'` (path alias) | Module path from `go.mod` — no config needed |
| All exports from a file visible | All **uppercase** identifiers exported; lowercase stays in package |

## Key insight

Go's project layout is deliberately "obvious" — directory = package, `internal/` = private, `cmd/` = binaries. Unlike TypeScript where you might have barrel files, re-exports, and path aliases scattered across config files, Go's structure is enforced by the compiler and discoverable from the filesystem alone. A developer unfamiliar with the codebase can instantly understand what's private and what's a binary entry point just by reading the directory tree.

## Zone of proximal development notes

Amit now has a complete, tested HTTP server. The final gap between prototype and production-grade was knowing *where to put things*. This lesson gives the full layout, cementing the mental model needed to scale the project confidently.

## What comes next

- **Goroutines & channels intro** (optional, mission-adjacent): Amit's mission scope said "beyond what's needed for an HTTP server," but a light intro to goroutines as the underlying model for Go's concurrency could be valuable context.
- **Capstone project**: Combine everything (layout, routing, JSON, middleware, errors, tests) into one well-structured mini project shipped as a binary — the original mission goal.
- **Database layer** (out of mission scope for now, but natural next step): `database/sql` + a driver, connecting the HTTP server to a real data store.
