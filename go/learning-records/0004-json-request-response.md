# Learning Record 0004 — JSON Request & Response

## Date
2026-06-16

## Lesson
0005 — JSON Request & Response

## What was taught

- `encoding/json` is a standard library package — no external dependency
- **Struct tags** (`\`json:"name"\``) control the JSON key for each field; without them, Go uses the PascalCase field name as-is
- Three essential tag patterns:
  - `` `json:"key"` `` — rename the field in JSON output
  - `` `json:"key,omitempty"` `` — omit the field when it holds the zero value
  - `` `json:"-"` `` — never marshal/unmarshal this field (use for sensitive data)
- **Decoding (request body → struct)**: `json.NewDecoder(r.Body).Decode(&input)`
  - Must pass a pointer (`&input`) so Decode can write into the variable
  - Always check the returned error — silently proceeding gives zero-value fields
- **Encoding (struct → response body)**: `json.NewEncoder(w).Encode(resp)`
  - Set `Content-Type: application/json` header **before** calling Encode
  - Prefer Encoder over `json.Marshal` for HTTP responses (streams directly, no copy)
- Ordering rule (reinforcing lesson 3): `w.Header().Set(...)` → `w.WriteHeader(code)` → body write
- `http.StatusCreated` (201) vs `http.StatusOK` (200) — use 201 for successful POST creates

## TS→Go comparisons taught

| Express/TypeScript | Go |
|---|---|
| `req.body as CreateUser` (via middleware) | `json.NewDecoder(r.Body).Decode(&input)` |
| `res.json(obj)` | `w.Header().Set("Content-Type","application/json"); json.NewEncoder(w).Encode(obj)` |
| Field name in type = JSON key (often same) | Struct tag required for explicit JSON key |
| `undefined` field = omitted (optional chain) | `omitempty` tag option |
| `@Exclude()` / separate response type | `` `json:"-"` `` tag |

## Key insight

Go's JSON handling is explicit by design: there is no `res.json()` magic — you set the Content-Type header and call the encoder yourself. This verbosity is intentional: it forces the developer to think about the response shape, headers, and status code as separate concerns. The struct tag system also makes the mapping between Go field names and JSON keys a static, compile-time property of the type, rather than a runtime convention.

## Zone of proximal development notes

Amit knows TypeScript interfaces and JSON serialisation well. The non-obvious parts:
1. Struct tags syntax (backticks, the exact `json:"..."` format)
2. The `&` pointer requirement for Decode
3. The Content-Type header order constraint (reinforces lesson 3)
4. The difference between `omitempty` and `json:"-"`

## What comes next

Lesson 0006: Middleware — writing a logging middleware, setting common headers, and chaining handlers. Builds directly on the handler pattern from lessons 3–5. Will show how to wrap handlers to avoid repeating `Content-Type` header setting.
