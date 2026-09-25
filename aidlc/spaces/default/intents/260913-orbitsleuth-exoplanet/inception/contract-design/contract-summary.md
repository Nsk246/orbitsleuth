# Contract Summary — OrbitSleuth

This document pins down every boundary between the 9 units [units], plus the
public HTTP API and the outbound third-party boundaries. The decisions come
from `contract-design-questions.md` [Q1–Q6].

## Contracts

| # | Provider Unit | Consumer | Mechanism | Owner |
|---|---------------|----------|-----------|-------|
| C1 | U5 backend-service | External: public web (U6 web-explore, U7 web-investigate) | Sync REST/HTTP `/api/v1`, JSON + gzip, polling | U2 api-contract |
| C2 | U3 analysis-core | U5, U8, U9 | In-process Python library interface | U3 (types); category enum from U2 |
| C3 | U4 guarded-services | U5 | In-process Python library interface | U4 (types); category enum from U2 |
| C4 | U8 curation-tool | U5 | Versioned data files (catalog manifest, curves, pre-computed runs), read at startup | U2 api-contract |
| C5 | U9 evaluation-tool | U5 | Versioned data file (evaluation report), read at startup | U2 api-contract |
| C6 | External: LLM provider | U4 | Outbound HTTPS via provider adapter | U4 (port); provider chosen in NFR Design |
| C7 | External: public light-curve archive | U8 (and U9 for sample curves) | Outbound HTTPS via archive adapter | U8 (port); archive chosen by spike US0.2 |
| C8 | U2 api-contract | U3–U9 | Shared schema (category vocabulary) | U2 api-contract |

## C1 — Public HTTP API (OpenAPI)

Rules:

- Every response is gzip-encoded when the client accepts it.
- The API never returns a review status or a "final" flag (AC0.6.5).
- Starting a run is asynchronous. The browser then polls about once a second
  for steps and status [Q1, Q2].

```yaml
openapi: 3.1.0
info: { title: OrbitSleuth API, version: 1.0.0 }
servers: [{ url: /api/v1 }]
paths:
  /catalog:
    get:
      summary: List curated catalog entries
      parameters:
        - { name: kind, in: query, schema: { enum: [real, synthetic, all], default: all } }
      responses:
        '200': { content: { application/json: { schema: { type: array, items: { $ref: '#/components/schemas/CatalogEntry' } } } } }
        default: { $ref: '#/components/responses/Error' }
  /catalog/{targetId}:
    get:
      summary: One catalog entry with provenance
      parameters: [{ $ref: '#/components/parameters/TargetId' }]
      responses:
        '200': { content: { application/json: { schema: { $ref: '#/components/schemas/CatalogEntry' } } } }
        '404': { $ref: '#/components/responses/Error' }
        '422': { $ref: '#/components/responses/Error' }
  /curves/{targetId}:
    get:
      summary: Curve data for display (raw or engine-detrended)
      parameters:
        - { $ref: '#/components/parameters/TargetId' }
        - { name: view, in: query, schema: { enum: [raw, detrended], default: raw } }
      responses:
        '200': { content: { application/json: { schema: { $ref: '#/components/schemas/Curve' } } } }
        '404': { $ref: '#/components/responses/Error' }
  /runs:
    post:
      summary: Start an analysis run (async)
      requestBody:
        content: { application/json: { schema: { $ref: '#/components/schemas/RunRequest' } } }
      responses:
        '200': { description: Served a stored pre-computed run (no live work counted), content: { application/json: { schema: { $ref: '#/components/schemas/RunAccepted' } } } }
        '202': { description: Live run accepted, content: { application/json: { schema: { $ref: '#/components/schemas/RunAccepted' } } } }
        '422': { $ref: '#/components/responses/Error' }
        '429':
          description: Live runs paused and no stored result exists
          headers: { Retry-After: { schema: { type: integer, description: seconds } } }
          content: { application/json: { schema: { $ref: '#/components/schemas/Error' } } }
  /runs/{runId}:
    get:
      summary: Run status, and the result once finished
      parameters: [{ $ref: '#/components/parameters/RunId' }]
      responses:
        '200': { content: { application/json: { schema: { $ref: '#/components/schemas/Run' } } } }
        '404': { $ref: '#/components/responses/Error' }
  /runs/{runId}/steps:
    get:
      summary: Trace steps after a given order number (polling)
      parameters:
        - { $ref: '#/components/parameters/RunId' }
        - { name: after, in: query, schema: { type: integer, minimum: 0, default: 0 } }
      responses:
        '200': { content: { application/json: { schema: { type: array, items: { $ref: '#/components/schemas/TraceStep' } } } } }
  /evaluation/latest:
    get:
      summary: Latest published evaluation report
      responses:
        '200': { content: { application/json: { schema: { $ref: '#/components/schemas/EvaluationReport' } } } }
        '404': { $ref: '#/components/responses/Error' }
  /health:
    get:
      summary: Health signal and error-rate counter
      responses:
        '200': { content: { application/json: { schema: { type: object, required: [status, errorCount], properties: { status: { enum: [ok, degraded] }, errorCount: { type: integer }, pipelineVersion: { type: string } } } } } }
components:
  parameters:
    TargetId: { name: targetId, in: path, required: true, schema: { type: string, pattern: '^[A-Za-z0-9][A-Za-z0-9._-]{0,63}$' } }
    RunId: { name: runId, in: path, required: true, schema: { type: string, format: uuid } }
  responses:
    Error: { description: Structured error, content: { application/json: { schema: { $ref: '#/components/schemas/Error' } } } }
  schemas:
    Category: { enum: [transit, eclipsing_binary, stellar_activity, noise] }
    CatalogEntry:
      type: object
      required: [targetId, kind, provenance, featured, thumbnail]
      properties:
        targetId: { type: string }
        kind: { enum: [real, synthetic] }
        featured: { type: boolean }
        thumbnail: { type: array, items: { type: number }, maxItems: 200 }
        provenance:
          oneOf:
            - { type: object, required: [archive, mission], properties: { archive: { type: string }, mission: { type: string } } }
            - { type: object, required: [injectedPeriodDays, injectedDepth, noiseLevel, seed], properties: { injectedPeriodDays: { type: number }, injectedDepth: { type: number }, noiseLevel: { type: number }, seed: { type: integer } } }
    Curve:
      type: object
      required: [targetId, view, time, flux, pointCount, downsampled]
      properties:
        targetId: { type: string }
        view: { enum: [raw, detrended] }
        time: { type: array, items: { type: number } }
        flux: { type: array, items: { type: number } }
        pointCount: { type: integer, description: points in the full curve }
        downsampled: { type: boolean, description: true when pointCount > 70000 (AC0.7.3) }
    RunRequest:
      type: object
      required: [targetId, mode]
      properties:
        targetId: { type: string }
        mode: { enum: [stored, live], description: stored = serve pre-computed; live = new run }
        parameters:
          type: object
          properties:
            detrendWindowHours: { type: number }
            periodMinDays: { type: number }
            periodMaxDays: { type: number }
        moreEvidenceFor: { type: string, format: uuid, description: run ID to extend (US5.4) }
    RunAccepted:
      type: object
      required: [runId, source]
      properties:
        runId: { type: string, format: uuid }
        source: { enum: [live, precomputed] }
        notice: { enum: [livePaused], description: set when a limit forced a stored result (AC3.5.1) }
    Run:
      type: object
      required: [runId, targetId, status, source, pipelineVersion, stale]
      properties:
        runId: { type: string, format: uuid }
        targetId: { type: string }
        status: { enum: [queued, running, succeeded, failed, interrupted] }
        source: { enum: [live, precomputed] }
        pipelineVersion: { type: string }
        stale: { type: boolean }
        parameters: { type: object }
        result: { $ref: '#/components/schemas/AnalysisResult' }
        failure: { $ref: '#/components/schemas/Error' }
    AnalysisResult:
      type: object
      required: [hypotheses, penalties]
      properties:
        hypotheses: { type: array, minItems: 4, maxItems: 4, items: { $ref: '#/components/schemas/Hypothesis' } }
        penalties: { type: array, items: { type: object, required: [check, points, reason], properties: { check: { type: string }, points: { type: number }, reason: { type: string } } } }
        fold: { type: object, properties: { periodDays: { type: number }, phase: { type: array, items: { type: number } }, flux: { type: array, items: { type: number } }, eventPhase: { type: number } } }
        explanation: { $ref: '#/components/schemas/Explanation' }
    Hypothesis:
      type: object
      required: [category, confidence, rank, evidence]
      properties:
        category: { $ref: '#/components/schemas/Category' }
        confidence: { type: number, minimum: 0, maximum: 100 }
        rank: { type: integer, minimum: 1, maximum: 4 }
        evidence: { type: array, minItems: 1, items: { $ref: '#/components/schemas/EvidenceItem' } }
    EvidenceItem:
      type: object
      required: [sourceToolOutputId, tool, check, value, effect]
      properties:
        sourceToolOutputId: { type: string }
        tool: { type: string }
        check: { type: string }
        value: { type: [number, string] }
        effect: { enum: [supports, weakens, notRun] }
        added: { type: boolean }
    Explanation:
      type: object
      required: [status]
      properties:
        status: { enum: [available, unavailable] }
        text: { type: string }
        usedToolOutputIds: { type: array, items: { type: string } }
    TraceStep:
      type: object
      required: [order, name, status]
      properties:
        order: { type: integer, minimum: 1 }
        name: { type: string }
        status: { enum: [ok, skipped, failed, notRun, running] }
        durationMs: { type: integer }
        keyInputs: { type: object }
        keyOutputs: { type: object }
        toolOutputId: { type: string }
        reason: { type: string }
    EvaluationReport:
      type: object
      required: [pipelineVersion, sampleVersion, mappingVersion, runDate, accuracy, perCategory, unmappedCount]
      properties:
        pipelineVersion: { type: string }
        sampleVersion: { type: string }
        mappingVersion: { type: string }
        runDate: { type: string, format: date }
        accuracy: { type: number }
        perCategory: { type: array, items: { type: object, required: [category, precision, recall, count], properties: { category: { $ref: '#/components/schemas/Category' }, precision: { type: number }, recall: { type: number }, count: { type: integer } } } }
        unmappedCount: { type: integer }
    Error:
      type: object
      required: [code, message, retryable]
      properties:
        code: { type: string, description: 'e.g. NOT_FOUND, INVALID_INPUT, ANALYSIS_FAILED, LIVE_RUNS_PAUSED, INTERNAL' }
        message: { type: string, description: plain language, no stack trace }
        runId: { type: string, format: uuid }
        retryable: { type: boolean }
```

Behavior notes for C1:

- **Failure vs low confidence (US3.4)**: a failed run returns
  `status: failed` with `failure.code = ANALYSIS_FAILED`, and it never carries
  a `result`. A low-confidence success is `status: succeeded` with a full
  result.
- **Output contract (TC-5)**: `Hypothesis.evidence` has `minItems: 1`, and
  `confidence` is bounded to 0–100 inclusive. The backend validates every
  result against this schema before serving it (AC0.6.2).
- **Evidence provenance (TC-4)**: every `sourceToolOutputId` must match a
  `TraceStep.toolOutputId` of the same run (AC0.6.3).
- **Browser retries** [Q5]: the browser retries only `retryable: true`
  errors and network failures, at most 2 times, with backoff at 1 s and 2 s.
  A 429 is never retried automatically; the browser shows the resume time
  from `Retry-After`.
- **Dropped polling (AC3.2.4)**: if 3 polls in a row fail, the browser shows
  "interrupted" and offers Retry. On the server, a run with no poll for 60 s
  keeps running and remains fetchable.

## C2 — Analysis core library interface (U3 → U5, U8, U9)

```yaml
shared-schema: analysis-core-interface
owner: u3-analysis-core
style: in-process Python; snake_case; no web-framework types
functions:
  run_pipeline:
    input:
      curve: "{time: list[float], flux: list[float]}"
      parameters: "{detrend_window_hours?, period_min_days?, period_max_days?}"
      extended_checks: "bool"
    output: "{tool_outputs: list[ToolOutput]}"
    raises: [InsufficientDataError, DetrendError, PeriodSearchError]   # typed domain errors, never None
  score:
    input: "{tool_outputs: list[ToolOutput]}"
    output: "{hypotheses: list[Hypothesis] (exactly 4), penalties: list[Penalty]}"
    raises: [OutputContractError]   # TC-5 violation is an error, never a partial result
  pipeline_version:
    output: "str"
types:
  ToolOutput: "tool_output_id: str; tool: str; parameters: dict; values: dict; status: pass|fail|not_run; reason: str | None; duration_ms: int"
  Hypothesis: "category: Category; confidence: float in [0, 100]; rank: int in [1, 4]; evidence: list[EvidenceItem] with at least 1 item"
  EvidenceItem: "source_tool_output_id: str; tool: str; check: str; value: float | str; effect: supports|weakens|not_run; added: bool"
  Penalty: "check: str; points: float; reason: str"
  Category: "imported from u2-api-contract (the only U2 dependency)"
guarantees:
  - deterministic at 6-decimal stored precision on the pinned platform
  - no review status and no "discovery" wording in any output
```

## C3 — Guarded services library interface (U4 → U5)

```yaml
shared-schema: guarded-services-interface
owner: u4-guarded-services
style: in-process Python; snake_case
functions:
  usage_guard.check_live_run:
    input: "visitor_key: str; now: datetime"
    output: "allowed: bool; retry_after_seconds: int | None; reason: rate_limit | daily_cap | None"
  usage_guard.record_live_run:
    input: "visitor_key: str; now: datetime"
  usage_guard.check_budget:
    input: "now: datetime"
    output: "allowed: bool"
  usage_guard.record_cost:
    input: "estimated_tokens: int; now: datetime"
  explanation.explain:
    input: "frozen_result: AnalysisResult; tool_outputs: list[ToolOutput]"
    output: "Explanation"
    raises: []   # never raises; returns status=unavailable on any failure
config_validated_at_startup_fail_fast:
  - "LIVE_RUNS_PER_HOUR (default 10), DAILY_SPEND_CAP, COST_PER_1K_TOKENS"
  - "LLM provider secret from environment only; if missing, explanations are unavailable"
provider_port_c6: "complete(prompt) -> text; 10 s timeout, 1 retry, then unavailable"
```

## C4 — Catalog and pre-computed data files (U8 → U5)

```yaml
shared-schema: catalog-data-files
owner: u2-api-contract
location: versioned data directory in the repository (path set in Functional Design)
files:
  manifest.json.gz:
    schema_version: 1
    entries: "list of {target_id, kind, featured, curve_file, checksum_sha256, provenance}"
  curves/<target_id>.json.gz:
    schema_version: 1
    fields: "target_id; time: list[float]; flux: list[float]; point_count"
  runs/<run_id>.json.gz:
    schema_version: 1
    fields: "run_id; target_id; pipeline_version; source: precomputed; parameters; trace_steps: list[TraceStep]; result: AnalysisResult"
rules:
  - U5 validates every file against its schema at startup and refuses to start on a mismatch
  - a checksum mismatch marks that entry unavailable and logs an error; other entries still load
  - a stored run whose pipeline_version differs from the running engine is served with stale=true
```

## C5 — Evaluation report file (U9 → U5)

```yaml
shared-schema: evaluation-report-file
owner: u2-api-contract
file: evaluation/latest.json.gz
schema_version: 1
fields: { pipeline_version, sample_version, mapping_version, run_date, accuracy, per_category: [ { category, precision, recall, count } ], unmapped_count }
rules:
  - U9 writes a new file per run and never overwrites a report from a different pipeline version without keeping the old one
  - U5 serves it read-only at GET /api/v1/evaluation/latest; a missing file returns 404 NOT_FOUND (the About view shows "No evaluation published yet")
```

## C6 / C7 — Outbound third-party boundaries

- **C6 LLM provider**: reached only through U4's provider port. There is a
  10 s timeout and 1 retry, then the explanation is marked unavailable. The
  provider is chosen in NFR Design, and the secret comes from the
  environment.
- **C7 Light-curve archive**: reached only through U8's archive adapter, and
  only in offline author runs. The archive is chosen and web-verified by
  spike US0.2.

## C8 — Shared vocabulary

```yaml
shared-schema: category-vocabulary
owner: u2-api-contract
Category: [transit, eclipsing_binary, stellar_activity, noise]
wire_form: identical strings (the enum values are already wire-safe)
python_form: an Enum imported by U3 and U4, which take nothing else from U2
typescript_form: generated union type used by U6 and U7
```

## Contract Ownership Rules

- **Specs and schemas**: U2 owns C1, C4, C5, and C8. U3 owns C2, and U4 owns
  C3, for the in-process types. U3 and U4 depend on U2 only for the category
  enum, never for wire formats [Q6].
- **Versioning**: the public API lives under `/api/v1`. Changes within v1 are
  additive only, meaning new optional fields and new endpoints. Clients
  ignore unknown fields. A breaking change requires `/api/v2`, with v1 kept
  until the frontend migrates [Q3]. Data files carry `schema_version`; a
  bump requires U5 to support both versions for one release.
- **Agreeing changes**: a breaking change is recorded as an ADR update. Both
  the provider and consumers must pass the updated U2 contract tests before
  merge. As a solo builder, this is the author's pre-merge check.
- **Enforcement**: the contract tests in U2 run in CI (AC0.3.3). A drift
  between engine output, API serialization, or the generated TypeScript types
  fails the build.

## Open Questions

| Contract | Question | Blocks |
|----------|----------|--------|
| C4, C5 | Exact repository path for the versioned data directory, and whether large curve files use Git LFS | U5, U8, U9 (Functional Design) |
| C1 | Parameter allowed ranges (`detrendWindowHours`, `periodMinDays`, `periodMaxDays`) for 422 validation | U5, U7 (Functional Design) |
| C3 | How the rate-limit `visitor_key` is derived (hashed IP vs anonymous cookie) | U4, U5 (Functional Design) |
| C6 | LLM provider, token pricing, and daily cap value | U4 (NFR Design) |
| C7 | Archive selection and access method | U8, U9 (spike US0.2) |

## Sources

- [units] `inception/units-generation/unit-of-work.md`, `unit-of-work-dependency.md`
- [components] `inception/domain-design/components.md`, `decisions.md`
- [requirements] `inception/requirements-analysis/requirements.md`
- [stories] `inception/user-stories/stories.md`
- [Q1]–[Q6] `inception/contract-design/contract-design-questions.md`

## Assumptions & Open Questions

- [assumption] The `targetId` pattern (alphanumeric plus `._-`, up to 64 characters) covers KOI and synthetic IDs. Confirm it against real IDs in spike US0.2.
- [assumption] The server keeps running a run whose polling stopped, for up to its 30 s budget. Confirm this in NFR Design.
- See the Open Questions table above.
