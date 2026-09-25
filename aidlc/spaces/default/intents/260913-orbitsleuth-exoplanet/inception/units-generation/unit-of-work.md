# Units of Work — OrbitSleuth

This document groups the 11 domain components [components] into 9 units of
work, following the approved decomposition plan [Q1–Q5]. Deployment model:

- One deployable backend service (a modular monolith).
- A static browser app.
- Two offline author tools.

The engine and scorer are an in-process library [Q1]. This document records
topology only. Build order is chosen in Delivery Planning.

## Unit Index

| Unit ID | Directory | Name | Kind | Deployment model | Size |
|---------|-----------|------|------|------------------|------|
| U1 | u1-repo-foundation | Repo foundation | packaging | Shared (repo-wide tooling) | S |
| U2 | u2-api-contract | API contract | spec | Shared (consumed in place by U5, U6, U7) | S |
| U3 | u3-analysis-core | Analysis core | library | Embedded (imported by U5, U8, U9) | L |
| U4 | u4-guarded-services | Guarded services | library | Embedded (imported by U5) | M |
| U5 | u5-backend-service | Backend service | service | Standalone (the one deployed backend) | L |
| U6 | u6-web-explore | Web explore | ui | Standalone (static browser app, shared shell) | L |
| U7 | u7-web-investigate | Web investigate | ui | Embedded in the U6 app shell | L |
| U8 | u8-curation-tool | Curation tool | packaging | Offline command-line tool (never deployed) | M |
| U9 | u9-evaluation-tool | Evaluation tool | library | Offline command-line tool (never deployed) | M |

## U1 — Repo foundation (`u1-repo-foundation`, packaging, S)

- **Description**: the repository skeleton and the quality gate that every
  other unit builds on.
- **Boundaries**:
  - Top-level backend/frontend directory split.
  - Lint and format config (Black, Ruff with `S` rules, Prettier, ESLint).
  - pytest and Vitest coverage config with the 80% floor.
  - GitHub Actions CI (no deploy step).
  - `pip-audit` and `npm audit`, the gitleaks pre-commit hook, and
    committed lockfiles [practices].
- **Responsibilities**: CI that fails on lint, test, coverage, vulnerable
  dependencies, and secrets.
- **Implementation notes**: no cloud resources (BC-2). Secret scanning is
  mandated [project Mandated]. The `evaluation` pytest marker is deselected
  by default (AC6.5.3).

## U2 — API contract (`u2-api-contract`, spec, S)

- **Description**: the single shared wire schema and vocabulary (ADR-007).
- **Boundaries**:
  - Payload schemas for catalog, curve, analysis result, hypothesis,
    evidence, trace step, error, and evaluation report.
  - The category enum (`transit`, `eclipsing_binary`, `stellar_activity`,
    `noise`).
  - camelCase wire names.
- **Responsibilities**: the source of truth for the frontend types and the
  backend serializers, plus the contract tests that detect drift (US0.3).
- **Implementation notes**: generated or shared types for TypeScript. The
  step-update transport (polling, SSE, or WebSocket) is chosen in Contract
  Design.

## U3 — Analysis core (`u3-analysis-core`, library, L)

- **Description**: AnalysisEngine and HypothesisScorer (ADR-001) as a plain
  Python package with no web-framework imports.
- **Boundaries**:
  - Detrending and BLS period search.
  - Vetting checks, including the extended checks (harmonic P/2 and 2P, and
    a wider period search).
  - Scoring and ranking of four categories.
  - The output contract (TC-4, TC-5).
- **Responsibilities**:
  - Deterministic outputs at stored precision.
  - Typed domain errors.
  - `not_run` checks and their named confidence penalties.
  - `sourceToolOutputId` provenance on every evidence item.
- **Implementation notes**: built fixtures-first [practices]. Thresholds and
  the tie-break rule are set in Functional Design. It must never emit a final
  status or "discovery" wording.

## U4 — Guarded services (`u4-guarded-services`, library, M)

- **Description**: ExplanationService and UsageGuard (ADR-004, ADR-006).
- **Boundaries**:
  - The provider-neutral LLM port and its adapter.
  - The runtime guard (number matching and forbidden terms).
  - The rate limit, daily spending cap, and cost meter, with an injectable
    clock.
- **Responsibilities**:
  - Explanations that degrade to "unavailable".
  - A best-effort per-visitor limit, with the hard daily cap as the real
    backstop.
  - Fail-fast configuration.
- **Implementation notes**: secrets come from the environment only. It uses a
  local counter store (no cloud). The LLM provider is chosen in NFR Design.

## U5 — Backend service (`u5-backend-service`, service, L)

- **Description**: the one deployable backend, containing WebApi,
  CatalogService, and RunService, wired to U3 and U4 in-process [Q1].
- **Boundaries**:
  - HTTP endpoints for catalog, curves, runs, trace steps, results, the
    evaluation report, and health.
  - Input validation and the structured error shape.
  - Run lifecycle, trace recording and replay, stale-version flags, and
    cached fallback.
  - Loading the versioned data files written by U8 and U9 at startup [Q5].
- **Responsibilities**:
  - Serves visitors.
  - Never computes review status.
  - Logs every error with its run ID.
  - Exposes the health signal and error-rate counter.
- **Implementation notes**: RunService code is also imported in-process by
  U8 for pre-computation, so pre-computed and live results share one shape
  (ADR-005). There is no deployment plan until Unit-of-Work approval (BC-2).

## U6 — Web explore (`u6-web-explore`, ui, L)

- **Description**: the browser app shell plus the exploration surfaces [Q3].
- **Boundaries**:
  - App shell, header, skip link, and design tokens.
  - Light-Curve Browser with filters, provenance, and the featured example.
  - LightCurveChart with raw, detrended, and folded views and keyboard and
    touch controls.
  - The About & accuracy view.
- **Responsibilities**:
  - Screen states, WCAG 2.1 AA, and responsive layout for these views.
  - Focus management across Browser and Workspace navigation.
  - Static glossary definitions.
- **Implementation notes**: renders only what the API returns. It uses
  headless primitives plus our own tokens [refined-mockups].

## U7 — Web investigate (`u7-web-investigate`, ui, L)

- **Description**: the investigation and review surfaces inside the
  Workspace [Q3].
- **Boundaries**:
  - HypothesisPanel (top card and ranked list).
  - EvidenceGroups and TermHelp.
  - TracePanel, RerunDisclosure, and ReviewActions.
  - NoteEditor and ReviewStore.
- **Responsibilities**:
  - The review state machine and the "final" transition (TC-6, ADR-002).
  - Browser persistence keyed by run ID.
  - Panel states: empty, loading, partial, failed, paused, and
    explanation unavailable.
- **Implementation notes**: hosts the TC-6 integration test and e2e smoke
  test 1.

## U8 — Curation tool (`u8-curation-tool`, packaging, M)

- **Description**: the offline CurationPipeline that produces the catalog
  data files (ADR-003) and runs the data-source spike.
- **Boundaries**:
  - Spike US0.2 to choose and verify the archive.
  - Seeded synthetic generator.
  - Archive adapter and fetcher.
  - Manifest with provenance and checksums.
  - Pre-computation through the RunService code.
- **Responsibilities**:
  - Versioned catalog and result files for U5 to load.
  - Non-zero exit when a target fails.
- **Implementation notes**: never deployed. Live archive access happens only
  here, never in pre-merge tests [practices].

## U9 — Evaluation tool (`u9-evaluation-tool`, library, M)

- **Description**: the offline EvaluationHarness (ADR-003).
- **Boundaries**:
  - Frozen KOI sample of about 150 targets with cached, checksummed curves.
  - Versioned label mapping, including the "unmapped" count.
  - Metric computation.
  - Report file for U5 to serve [Q5].
- **Responsibilities**:
  - Reproducible baseline metrics.
  - A 3-row pre-merge fixture test.
- **Implementation notes**: excluded from the default pre-merge suite. It
  uses the archive chosen in spike US0.2 (see the Assumptions below).

## Sources

- [components] `inception/domain-design/components.md`
- [decisions] `inception/domain-design/decisions.md`
- [stories] `inception/user-stories/stories.md`
- [requirements] `inception/requirements-analysis/requirements.md`
- [practices] `inception/practices-discovery/team-practices.md`
- [refined-mockups] `inception/refined-mockups/design-system-mapping.md`
- [project Mandated] `aidlc/spaces/default/memory/project.md`
- [Q1]–[Q5] `inception/units-generation/units-generation-questions.md`

## Assumptions & Open Questions

- [assumption] Spike US0.2 (in U8) produces a one-day decision note, not
  code. U9 needs its archive choice to cache sample curves, so Delivery
  Planning must schedule US0.2 before U9's curve caching. No code dependency
  from U9 to U8 is modeled.
- Open: the step-update transport and the report-file format are pinned in
  Contract Design.
