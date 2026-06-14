# Learning Record 0003 — Routing & Path Parameters

## Date
2026-06-14

## Lesson
0004 — Routing & Path Parameters

## What was taught

- `http.NewServeMux()` creates an isolated mux — pass it to `http.ListenAndServe(":8080", mux)` instead of `nil`
- Why DefaultServeMux is problematic in real programs: global state, third-party route pollution
- **Exact vs. subtree patterns**: `"/users"` = exact match only; `"/users/"` = subtree (matches `/users/` and any sub-path)
- Go 1.22 enhanced patterns: prefix with method to restrict to a single verb — `"GET /users/{id}"`
  - Wrong-method requests are automatically rejected with 405 Method Not Allowed (no manual `r.Method` check needed)
- Path parameter wildcards: `{name}` syntax in pattern string
- `r.PathValue("name")` — the correct way to extract a path parameter from inside a handler
- `http.Error(w, message, code)` — convenience function for writing an error response (sets status + plain-text body)

## TS→Go comparisons taught

| Express (TS) | Go (net/http 1.22+) |
|---|---|
| `const app = express()` | `mux := http.NewServeMux()` |
| `app.get('/users/:id', h)` | `mux.HandleFunc("GET /users/{id}", h)` |
| `req.params.id` | `r.PathValue("id")` |
| Express auto-404 on unmatched method | Go 1.22+ ServeMux auto-405 |
| `app.listen(8080)` | `http.ListenAndServe(":8080", mux)` |

## Key insight

Before Go 1.22, routing was commonly delegated to third-party libraries (gorilla/mux, chi, httprouter) because the standard ServeMux lacked method matching and named path parameters. Since Go 1.22, the standard library handles the majority of real-world routing needs without any external dependencies. Amit should use Go 1.22+ patterns in the server he's building — no router library needed.

## Zone of proximal development notes

Amit knows Express routing well; the `{name}` wildcard and `r.PathValue` map directly to `:param` and `req.params`. The non-obvious parts are:
1. Exact vs. subtree distinction (trailing slash matters)
2. That method prefixing in the pattern string is a 1.22 feature — older Go code he reads in the wild will manually check `r.Method`

## What comes next

Lesson 0005: JSON Request & Response — using `encoding/json` to decode a request body into a struct and encode a struct back as a JSON response. Introduces struct tags (`json:"name"`). Builds directly on the routing patterns from this lesson.
