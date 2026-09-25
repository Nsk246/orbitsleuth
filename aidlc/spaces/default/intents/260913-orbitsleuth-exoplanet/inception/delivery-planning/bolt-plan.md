# Bolt Plan — OrbitSleuth

A **Bolt** is one build pass over one or more units of work. Each ends in
something that runs and can be demonstrated. This plan orders 7 Bolts over
the 9 units [units], value-first within the dependency map [Q1] [scope Q4].
It keeps one hard rule: the analysis output shape is defined before the
hypothesis display [scope Q5].

**Construction settings:**

- **Build order across units**: one unit at a time. Each unit is designed
  and built completely, in the Bolt order below [Q5].
- **Walking skeleton**: none. A walking skeleton is a minimal end-to-end
  slice built first. Team practice skips it, so B1 runs like any other Bolt
  [practices].
- **Branching**: one short-lived branch per Bolt, squash-merged to `main`
  [practices].
- **Staffing**: a single builder in this session, with the author approving
  each step [Q6].

## Bolt Sequence

### B1 — Foundation and contract

- **Units**: U1 repo-foundation, U2 api-contract.
- **Also pulled forward**: spike story US0.2 (data-source choice, owned by
  U8), run as a one-day research task inside B1's timeframe [Q4]. No U8
  code is written.
- **Walking skeleton**: no.
- **Definition of Done**:
  - CI runs Black, Ruff (`S` rules), Prettier, ESLint, pytest and Vitest
    with the 80% floor, `pip-audit` and `npm audit`, and gitleaks, and it
    passes on an empty skeleton.
  - The U2 schema covers every C1/C4/C5/C8 shape, with a contract test that
    fails on drift.
  - Generated TypeScript types exist.
  - The US0.2 decision note names the archive, the access method, and at
    least two alternatives, each web-verified.
- **Confidence hypothesis**: the quality gate and the shared contract are
  enough for later Bolts to build in parallel without wire-format drift.
- **Expected demo**: a green CI run on GitHub, the contract test failing on
  a deliberately broken payload, and the data-source decision note.

### B2 — Web explore

- **Units**: U6 web-explore.
- **Walking skeleton**: no.
- **Definition of Done**:
  - Browser, filters, provenance, chart (raw view, zoom and pan, keyboard
    and touch controls), and the About & accuracy view with its empty state.
  - Built against a fixture catalog and a mocked API generated from the U2
    contract.
  - axe-core scan clean; responsive at 320 / 768 / 1024 px.
  - AC2.1.2 performance check run on the rig.
- **Confidence hypothesis**: the dark, accessible visual design and a
  70,000-point interactive chart hold up in a real browser. That settles the
  frontend-performance risk early.
- **Expected demo**: browse the synthetic fixture curves, open one, and zoom
  and pan smoothly, including on a phone-sized screen.

### B3 — Analysis core

- **Units**: U3 analysis-core.
- **Walking skeleton**: no.
- **Definition of Done**:
  - Fixtures-first tests pass for detrending, BLS period recovery, the five
    vetting checks, the extended checks, scoring and ranking, and the
    `not_run` penalty rule.
  - The TC-4 provenance test and the TC-5 contract test pass.
  - The eclipsing-binary fixtures rank correctly.
  - Output is deterministic at stored precision.
  - **Plus**: a one-off, explicitly labeled small-sample validation run
    (about 10 KOI targets from the archive chosen in B1) outside the
    pre-merge suite, with results noted in the Bolt summary [Q4].
- **Confidence hypothesis**: rule-based scoring on real tool outputs can
  separate transits from eclipsing binaries and noise well enough to be
  worth a baseline. If the small-sample run looks poor, tuning happens
  before the backend and UI build on it.
- **Expected demo**: run the engine from a notebook or CLI on a synthetic
  transit, an eclipsing binary, and a noise curve, and see four ranked
  hypotheses with evidence linked to tool outputs.

### B4 — Guarded services and backend

- **Units**: U4 guarded-services, U5 backend-service.
- **Walking skeleton**: no.
- **Definition of Done**:
  - `/api/v1` implements C1.
  - Async runs with polling steps.
  - Structured errors, including failure vs low confidence.
  - Rate limit and daily cap with injectable-clock tests.
  - Explanation guard (number match and forbidden terms) with the
    "unavailable" fallback; this works without an LLM key [Q3].
  - Health endpoint and error counter; run ID in every error log.
  - Loads C4/C5 fixture data files at startup.
  - Contract tests green.
- **Confidence hypothesis**: the backend can serve pre-computed runs within
  10 s and fresh runs within 30 s, never emits a final status, and degrades
  cleanly when the LLM or the limits say no.
- **Expected demo**: `curl` a run end to end, then force a failure, a
  low-confidence result, and a paused live run, and show three distinct,
  correct responses.

### B5 — Web investigate (full investigation loop)

- **Units**: U7 web-investigate.
- **Walking skeleton**: no.
- **Definition of Done**:
  - Hypothesis panel with the top card and ranked list; evidence groups and
    term popovers.
  - Trace panel with live polling and replay.
  - Adjust and re-run; request more evidence.
  - Accept, reject, change, and clear decisions, and notes, persisted by run
    ID.
  - The TC-6 integration test passes.
  - E2E smoke 1 passes: browse, open, analyze, review evidence, accept, and
    the decision survives a reload.
  - axe-core scan clean on all panel states.
- **Confidence hypothesis**: a non-expert recruiter can go from landing to
  an evidence-backed, human-reviewed result in 3 clicks, and nothing ever
  becomes final without a human action.
- **Expected demo**: the complete core loop in the browser against the local
  backend with fixture data. This is the portfolio's headline demo.

### B6 — Curation (real catalog)

- **Units**: U8 curation-tool (US6.1–US6.3; US0.2 already done in B1).
- **Walking skeleton**: no.
- **Definition of Done**:
  - Seeded synthetic generator.
  - Archive adapter for the source chosen in B1; a failed target makes the
    run exit non-zero.
  - Manifest with checksums.
  - Pre-computed runs written through RunService code.
  - U5 loads the real catalog of 20–50 curves.
  - The featured example is chosen and marked.
- **Confidence hypothesis**: real archival curves flow through the same
  pipeline and UI with no special cases, and every catalog entry is
  reproducible from its manifest.
- **Expected demo**: the live site shows real Kepler or TESS curves next to
  synthetic ones, and the featured-example CTA reaches a result.

### B7 — Evaluation (the rigor claim)

- **Units**: U9 evaluation-tool.
- **Walking skeleton**: no.
- **Definition of Done**:
  - Frozen sample of about 150 KOI targets with cached, checksummed curves.
  - Versioned label mapping with an "unmapped" count.
  - Metrics computed and published to `evaluation/latest.json.gz`.
  - Identical metrics on a re-run.
  - The 3-row fixture test runs pre-merge.
  - The About & accuracy view shows the real baseline.
- **Confidence hypothesis**: OrbitSleuth's accuracy claim is measurable,
  reproducible, and honestly reported, including its weak spots.
- **Expected demo**: the About & accuracy page shows real per-category
  precision and recall with the pipeline version and date, and a re-run
  reproduces them.

## Order Check Against the Dependency Map

| Bolt | Units | Needs (from dependency map) | Satisfied by |
|------|-------|-----------------------------|--------------|
| B1 | U1, U2 | — ; U1 | B1 (U1 before U2 inside the Bolt) |
| B2 | U6 | U1, U2 | B1 |
| B3 | U3 | U1, U2 | B1 |
| B4 | U4, U5 | U1, U2, U3 | B1, B3 |
| B5 | U7 | U6 (plus U5 for real data) | B2, B4 |
| B6 | U8 | U3, U5 | B3, B4 |
| B7 | U9 | U3 | B3 |

Every Bolt's dependencies are met by earlier Bolts. Hard rule check: the
analysis output shape (U2 in B1, U3 in B3) precedes the hypothesis display
(U7 in B5) ✓. B2 and B3 are independent and could overlap. With one builder
they run in the order shown.

## Sources

- [units] `inception/units-generation/unit-of-work.md`, `unit-of-work-dependency.md`, `unit-of-work-story-map.md`
- [contracts] `inception/contract-design/contract-summary.md`
- [stories] `inception/user-stories/stories.md`
- [scope] `ideation/scope-definition/scope-document.md`
- [practices] `inception/practices-discovery/team-practices.md`
- [Q1]–[Q6] `inception/delivery-planning/delivery-planning-questions.md`

## Assumptions & Open Questions

- [assumption] The small-sample validation run in B3 depends on the archive chosen in B1's spike. If the spike slips, B3 ships with fixtures only and the run moves to B6 [Q3].
