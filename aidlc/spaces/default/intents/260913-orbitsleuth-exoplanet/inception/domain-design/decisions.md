# Architecture Decision Records — Domain Design

Status of every ADR below: **Accepted** (2026-09-25, confirmed in
`domain-design-questions.md`). Each ADR states its security implications, as
the inception architecture rules require.

## ADR-001: Hypothesis scoring is a separate building block from the analysis engine

- **Context**: Scoring turns vetting outputs into four ranked hypotheses and
  enforces the evidence-and-confidence contract (TC-4, TC-5; US0.6).
  Thresholds will be tuned against the KOI evaluation. The signal-processing
  tools change rarely. The engine is tested fixtures-first [practices].
- **Decision**: Two building blocks. AnalysisEngine produces ToolOutputs.
  HypothesisScorer consumes them and owns Hypothesis and EvidenceItem, the
  ranking, and output-contract validation [Q1].
- **Consequences**:
  - (+) Tuning scores never touches tool code.
  - (+) The TC-4/TC-5 tests live in one component.
  - (+) The evaluation can re-score cached tool outputs.
  - (−) One more interface, and the ToolOutput shape becomes a contract
    between the two.
  - Security: the contract validation acts as a safety gate, so a
    malformed hypothesis never reaches a visitor.
- **Alternatives Rejected**: One combined AnalysisEngine. It has fewer
  boundaries, but it mixes two change rates and spreads the contract checks
  across tool code.

## ADR-002: A browser-side ReviewStore alone owns the "final" review status

- **Context**: Review decisions live only in the visitor's browser (FR6.6).
  The API must never emit a final status (AC0.6.5). The user-stories review
  left the owner of the final transition open (AC5.1.3). TC-6 needs one
  testable owner.
- **Decision**: ReviewStore, running in the browser, is the only code that
  can set accepted or rejected. The latest human action wins, including
  Clear [Q2] [stories Q7].
- **Consequences**:
  - (+) One state machine, and the TC-6 integration test targets it.
  - (+) No server state for visitors and no accounts.
  - (−) Decisions are per-browser and not shared, which was accepted in
    requirements Q4.
  - Security: no server write endpoint exists for review data, which
    removes a spam and tampering surface. Notes render as plain text to
    prevent script injection.
- **Alternatives Rejected**: A backend review endpoint that validates
  transitions. It would give two sources of truth to keep in sync, add a
  server write surface, and contradict browser-only storage.

## ADR-003: Curation and evaluation are offline command-line building blocks

- **Context**: Catalog building, pre-computation (US6.1–US6.3), and KOI
  evaluation (US6.4–US6.5) are author-only. Cost must stay minimal (BC-1).
  No cloud resources exist before Unit-of-Work approval (BC-2).
- **Decision**: CurationPipeline and EvaluationHarness run from the command
  line and call the engine and scorer directly. They are never exposed to
  visitors. WebApi only reads the latest published EvaluationReport [Q3].
- **Consequences**:
  - (+) Nothing admin-shaped exists in the public service.
  - (+) Zero hosting cost for these jobs.
  - (+) Runs are reproducible from the repository.
  - (−) The author runs them manually; there is no scheduled refresh.
  - Security: no admin authentication is needed, because there is no admin
    endpoint. Archive fetches happen only on the author's machine or in CI.
- **Alternatives Rejected**: Admin features inside the public backend. They
  would need authentication (accounts are out of scope), enlarge the attack
  surface, and add cost.

## ADR-004: The LLM explanation is isolated in ExplanationService behind a provider-neutral port

- **Context**: The explanation is optional (Should Have), paid,
  non-deterministic, and must never alter or contradict tool outputs. It
  must never use "discovery" wording and must degrade to "unavailable"
  (FR4.5–FR4.7; US4.3, US4.4).
- **Decision**: ExplanationService owns the provider adapter, the runtime
  number and forbidden-term guard, cost reporting to UsageGuard, and the
  degradation path. It runs only on a frozen result [Q4].
- **Consequences**:
  - (+) The provider can be swapped or disabled with no analysis change.
  - (+) The guard lives in one place.
  - (+) The LLM-disabled equivalence test (AC4.2.2) is natural.
  - (−) One more component, plus an adapter to maintain.
  - Security: the provider secret is read from the environment and scoped
    to this component only. Model output is untrusted and is shown only
    after the guard passes.
- **Alternatives Rejected**: An explanation step inside RunService. It would
  couple orchestration to a third-party API and scatter the guard and
  degradation logic.

## ADR-005: RunService is the single owner of runs, traces, and stored results

- **Context**: Curated curves use pre-computed results with trace replay
  (US3.1, US6.3). Live runs, re-runs, and requests for more evidence create
  new runs (US3.2, US5.3, US5.4). Every run needs a run ID for reproduction
  and logging (US6.7).
- **Decision**: RunService owns AnalysisRun and TraceStep for both live and
  pre-computed runs. CurationPipeline writes pre-computed results through it
  [Q5].
- **Consequences**:
  - (+) One result shape and one lookup.
  - (+) Cache fallback and stale-version flags live in one place.
  - (−) CurationPipeline depends on RunService rather than writing files
    directly.
  - Security: run records hold no personal data. The visitor key never
    enters RunService's records; it stays in UsageGuard.
- **Alternatives Rejected**: The catalog owning pre-computed results. That
  would give two result shapes, two lookups, and duplicated staleness logic.

## ADR-006: UsageGuard is a separate building block for rate limits, the spending cap, and the cost meter

- **Context**: The site is public with no accounts. Live runs and paid LLM
  calls both need gating (FR9, NFR8, US6.6). The rules need boundary tests
  with a controllable clock.
- **Decision**: UsageGuard owns UsageCounter and SpendLedger. RunService and
  ExplanationService consult it before live or paid work [Q6].
- **Consequences**:
  - (+) The policy lives in one place, is unit-testable with an injectable
    clock, and is shared by both callers.
  - (+) The API stays thin.
  - (−) The per-visitor limit is best-effort and can be evaded. The daily
    cap is the hard guard.
  - Security: the visitor key is hashed and never logged raw. The service
    fails fast on missing or invalid configuration.
- **Alternatives Rejected**: Limits inside the API request handlers. They
  would be duplicated for the explanation path and harder to test in
  isolation.

## ADR-007: WebApi is the only translation boundary between the backend and the browser

- **Context**: The team practice fixes a plain analysis engine, camelCase
  on the wire, and one shared vocabulary (FR4.8, Q8 of practices). The
  frontend never recomputes (FR5.3).
- **Decision**: WebApi alone converts internal models to the wire schema,
  validates all inputs, and emits one structured error shape and the health
  signals.
- **Consequences**:
  - (+) Contract drift is caught by a single contract test (US0.3).
  - (+) Backend building blocks stay transport-free.
  - (−) WebApi touches many components and must stay a thin adapter.
  - Security: all visitor input validation and error sanitization happen at
    one boundary, and stack traces never cross it.
- **Alternatives Rejected**: Each backend component exposing its own HTTP
  surface. That multiplies validation points and causes vocabulary drift.

## ADR-008: The real-curve archive is an external dependency behind an adapter; the source is chosen by spike US0.2

- **Context**: The data source was intentionally left open [project
  correction], and any named source must be web-verified [project
  correction]. Stories created a one-day spike (US0.2).
- **Decision**: Domain Design names no specific archive. CurationPipeline
  reaches it through an archive adapter, and EvaluationHarness fetches the
  KOI table once, then versions it.
- **Consequences**:
  - (+) The component boundaries hold for any archive.
  - (−) Fetch details stay open until the spike completes.
  - Security: fetches are outbound only and author-run, so no visitor
    traffic reaches the archive.
- **Alternatives Rejected**: Fixing an archive now from training knowledge.
  That was rejected by the project correction requiring verification first.

## Sources

- [requirements] `inception/requirements-analysis/requirements.md`
- [stories] `inception/user-stories/stories.md`, `user-stories-questions.md`
- [practices] `inception/practices-discovery/team-practices.md`
- [project correction] `aidlc/spaces/default/memory/project.md` ## Corrections
- [Q1]–[Q6] `inception/domain-design/domain-design-questions.md`

## Assumptions & Open Questions

None.
