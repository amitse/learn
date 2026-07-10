# Learning Record 0013 — database/sql + pgx: Connecting Postgres

## Date
2026-07-10

## Lesson
0014 — database/sql + pgx: Connecting Postgres to Your HTTP Server

## What was taught

- **database/sql**: Go's standard library DB abstraction; works with any driver via blank import registration.
- **pgx/v5 as database/sql driver**: `_ "github.com/jackc/pgx/v5/stdlib"` registers the driver as a side effect.
- **sql.Open vs db.Ping()**: `Open` only validates config; `Ping()` is the actual dial. Always ping on startup.
- **QueryRowContext + Scan**: Scanning columns directly into Go struct fields; replaces accessing `rows[0]` in Node.
- **sql.ErrNoRows**: Sentinel error from `Scan()` when no row was found — map to 404 not 500.
- **QueryContext + rows.Next()**: Iterating multiple rows; must `defer rows.Close()` and check `rows.Err()` after loop.
- **RETURNING clause**: Postgres idiom to get the inserted row back in one round-trip — no separate SELECT needed.
- **Connection pool settings**: `SetMaxOpenConns`, `SetMaxIdleConns`, `SetConnMaxLifetime` — tune once in `main.go`.
- **Store interface swap**: Replacing the in-memory `map + sync.RWMutex` store with a DB-backed store — handlers unchanged because both satisfy the same interface.

## TS → Go comparisons taught

| TypeScript (pg / node-postgres) | Go (database/sql + pgx) |
|---|---|
| `new Pool({ connectionString })` | `sql.Open("pgx", os.Getenv("DATABASE_URL"))` + `db.Ping()` |
| `await pool.query(sql, [params])` | `db.QueryRowContext(ctx, sql, params).Scan(&fields...)` |
| `rows[0]` | `.Scan()` populates struct fields directly |
| `rows.length === 0` check | `err == sql.ErrNoRows` |
| `await pool.query("SELECT …")` → `rows` array | `db.QueryContext` → `rows.Next()` loop + `rows.Err()` |
| `INSERT … RETURNING` (same in pg) | `INSERT … RETURNING` + `QueryRowContext` + `Scan` |

## Key insight

The store interface is the seam between HTTP and DB. Handlers call `store.GetTask(ctx, id)` regardless of whether the store is backed by a map or Postgres. Swapping persistence is a store-layer concern, not a handler concern — this is why the handler struct pattern from Lesson 13 pays off immediately.

## Zone of proximal development notes

Amit has now completed the full HTTP-server-to-DB path. Logical next lessons:
- **Schema migrations** (golang-migrate / Atlas) — how DB schema evolves over time
- **pgxpool** — native pgx pool for performance-critical services
- **JWT middleware** — adding auth to the existing middleware chain
