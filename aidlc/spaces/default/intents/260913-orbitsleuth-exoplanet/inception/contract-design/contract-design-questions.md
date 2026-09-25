# Contract Design — Clarifying Questions

## Sources

- [units] `inception/units-generation/unit-of-work.md`, `unit-of-work-dependency.md` (9 units; integration table)
- [components] `inception/domain-design/components.md`
- [requirements] `inception/requirements-analysis/requirements.md` (NFR1 ≤ 10 s pre-computed / ≤ 30 s fresh; FR9 limits)
- [stories] `inception/user-stories/stories.md` (AC0.7.2 compact curve payload; US3.2 live trace; AC3.2.4 dropped connection)

Already settled and not re-asked:

- HTTP between the browser and the backend.
- In-process calls to the analysis core.
- Offline tools write versioned data files that the backend reads at startup.
- camelCase on the wire, with one shared vocabulary.
- One structured error shape, and no final status from the API.

## Q1. How does the browser receive live trace steps?

Open since Domain Design (US3.2). A fresh run can take up to 30 seconds. The
future AWS path favors short, stateless requests.

- A. Polling: the browser asks for new steps about once a second; simplest and works on any host, including serverless
- B. Server-Sent Events (one open stream per run): smoother, but needs long-lived connections
- C. WebSocket: two-way, more than this needs
- X. Other (please specify)

[Answer]: A (2026-09-25T15:00:17Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q2. Is starting an analysis synchronous or asynchronous?

- A. Asynchronous: "start run" returns a run ID immediately (HTTP 202); the browser then polls status, steps, and the result by run ID
- B. Synchronous: the request waits up to 30 seconds and returns the full result
- X. Other (please specify)

[Answer]: A (2026-09-25T15:00:17Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q3. How are API versions handled?

The only consumer today is our own frontend, but the API is public on the
internet.

- A. A `/api/v1` path prefix; changes within v1 are additive only (new optional fields; clients ignore unknown fields); any breaking change means `/api/v2`
- B. No version prefix; the frontend and backend always deploy together
- X. Other (please specify)

[Answer]: A (2026-09-25T15:00:17Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q4. What format carries curve data on the wire and in the data files?

A 70,000-point curve must travel compactly (AC0.7.2). The offline tools write
catalog and curve files for the backend [units Q5].

- A. JSON with parallel arrays (`time[]`, `flux[]`, rounded to stored precision) and gzip compression on the wire; the data files use the same JSON shape plus a schema version, gzipped on disk
- B. A binary columnar format (for example Apache Arrow or Parquet) for both the wire and the files
- X. Other (please specify)

[Answer]: A (2026-09-25T15:00:17Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q5. How are errors, timeouts, and retries handled?

- A. Every error uses one shape: `code`, plain-language `message`, `runId` if any, and `retryable` (true/false). The browser retries only retryable errors, at most 2 times with backoff. The backend gives the LLM provider a 10-second timeout and 1 retry, then marks the explanation unavailable. Rate-limited requests get HTTP 429 with a `Retry-After` header
- B. The browser never retries automatically; the visitor presses Retry
- X. Other (please specify)

[Answer]: A (2026-09-25T15:00:17Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q6. Who owns each contract spec?

- A. U2 (API contract) owns the HTTP spec and the data-file schemas; the backend (U5), the frontends (U6, U7), and the offline tools (U8, U9) are consumers that must pass U2's contract tests. U3 and U4 share only the category vocabulary enum from U2, never the wire format (this also clarifies review finding R-02 from Units Generation)
- B. Each provider unit owns its own spec
- X. Other (please specify)

[Answer]: A (2026-09-25T15:00:17Z, **Mode:** chat — user accepted the recommended option: "all good")

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Live trace steps are delivered by polling, about once a second (Q1: A)
- Starting a run is asynchronous: HTTP 202 with a run ID, then polling for status, steps, and result (Q2: A)
- Versioning: a `/api/v1` prefix; additive-only changes within v1; breaking changes go to `/api/v2` (Q3: A)
- Curves: JSON parallel arrays at stored precision, gzip on the wire; data files use the same shape plus a schema version, gzipped (Q4: A)
- Errors: one shape (`code`, `message`, `runId`, `retryable`); the browser retries retryable errors at most 2 times with backoff; LLM gets a 10 s timeout and 1 retry, then "unavailable"; 429 with `Retry-After` for limits (Q5: A)
- U2 owns the HTTP spec and data-file schemas; U5–U9 are consumers bound by U2's contract tests; U3 and U4 use only the category enum (Q6: A)

Does this all look correct before I generate the contract summary?

- Looks correct
- Request changes

[Answer]: Looks correct
