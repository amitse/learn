# Learning Record 0014 — Schema Migrations with golang-migrate

## Date
2026-07-13

## Lesson
0015 — Schema Migrations with golang-migrate

## What was taught

- **Why migrations**: Manual SQL setup breaks in multi-env/multi-dev scenarios; versioned SQL scripts in Git are the solution.
- **File naming convention**: `NNNNNN_<name>.up.sql` / `NNNNNN_<name>.down.sql` pairs in a `migrations/` directory.
- **CLI usage**: `migrate up`, `migrate down N`, `migrate version`, `migrate force N`.
- **schema_migrations table**: Auto-created by golang-migrate to track current version and dirty state.
- **Dirty flag**: Set when a migration crashes mid-flight. Recovery: fix DB manually → `migrate force <prev-version>`.
- **Programmatic usage**: `migrate.New("file://migrations", dbURL)` + `m.Up()` — call before starting HTTP server.
- **Blank driver imports**: `_ "…/database/postgres"` and `_ "…/source/file"` register drivers via `init()` — same pattern as `database/sql`.
- **ErrNoChange**: Not an error — means all migrations already applied.

## TS → Go comparisons taught

| TypeScript (Prisma / Drizzle) | Go (golang-migrate) |
|---|---|
| Schema defined in TS/Prisma DSL | Plain SQL files |
| `npx prisma migrate dev` | `migrate -path ./migrations -database $URL up` |
| ORM generates SQL | You write SQL |
| Framework tracks applied migrations | `schema_migrations` table |

## Key insight

Migrations are code — they live in Git, get reviewed in PRs, and are applied deterministically in every environment. The blank import pattern for the postgres and file source drivers is the same `init()` registration used by `database/sql` drivers. `ErrNoChange` must be explicitly ignored: it is informational, not a failure.

## Zone of proximal development notes

Amit now has the full DB layer (connect → query → migrate). Natural next:
- **pgxpool** — native pgx pool bypasses `database/sql` overhead for high-throughput services
- **JWT middleware** — adds auth to the middleware chain (Lesson 0006)
- **testcontainers-go** — integration-testing DB handlers with a real ephemeral Postgres
