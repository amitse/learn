# Learning Record 0066 — Table-Test Request-ID Propagation Cases

## Date
2026-10-21

## Lesson
0066 — Table-Test Request-ID Propagation Cases

## What was taught

- A request-ID propagation test should represent the meaningful selection branches: trusted-valid upstream value, trusted-invalid upstream value, untrusted valid-looking value, and missing upstream value.
- Each table row has one expected selected ID. The test executes the real middleware and asserts that both middleware-owned outputs—the `X-Request-ID` response header and a decoded private error-log event—equal that value.
- A valid format is insufficient for reuse: without the server-owned trusted-proxy marker, the valid-looking client value is discarded in favor of the generated fallback.
- Keep the test focused: the validator’s own unit tests cover detailed malformed inputs; the propagation table covers selection branches and cross-output wiring.
- Create a fresh buffer-backed JSON logger within each subtest so a log event from one row cannot affect another row’s decoding.

## TS → Go comparison

| TypeScript / Node | Go |
|---|---|
| `it.each(cases)` | `for _, tt := range tests { t.Run(tt.name, ...) }` |
| request fixture plus captured structured log | `httptest.NewRequest`, `httptest.NewRecorder`, and a buffer-backed `slog.NewJSONHandler` |
| `expect(header).toBe(want)` and `expect(event.request_id).toBe(want)` | exact header comparison and a decoded narrow event struct comparison |

## Key insight

The four-row table is a specification for correlation continuity: provenance and format decide one selected ID, and every observable output owned by the middleware must report that same value. Testing only a header or only a log can miss a split-brain correlation bug.

## Zone of proximal development notes

Amit has already isolated trust, bounded the accepted ID grammar, injected deterministic generation, and tested one invalid trusted value end to end. This lesson generalizes that known test shape into a small table without introducing tracing infrastructure, metrics, or a new middleware abstraction.

## What comes next

- Add a separate adapter if an upstream standard trace context must be extracted or emitted; do not conflate its grammar or trust model with `X-Request-ID`.
- Consider a low-cardinality rejection metric at an existing metrics boundary, without using raw rejected IDs as labels.
- If the service calls downstream HTTP dependencies, propagate the already-selected request ID deliberately at that outbound boundary and test it separately.
