# Units Generation — Clarifying Questions

## Sources

- [components] `inception/domain-design/components.md` (11 components)
- [decisions] `inception/domain-design/decisions.md` (ADR-001 to ADR-008)
- [stories] `inception/user-stories/stories.md` (36 stories)
- [requirements] `inception/requirements-analysis/requirements.md`
- [practices] `inception/practices-discovery/team-practices.md` (solo builder; single repository with a backend/frontend split)

This stage groups components into Units of Work and maps the dependencies
between them. It does not choose build order; that is Delivery Planning's job.

## Q1. How is the backend deployed?

The backend components are WebApi, CatalogService, RunService, UsageGuard,
and ExplanationService. AnalysisEngine and HypothesisScorer are plain Python
packages (ADR-001, ADR-007).

- A. One deployable backend service (a modular monolith) that contains the five backend components; the engine and scorer are an in-process library it imports
- B. Several independently deployed services (for example a separate analysis service and a separate API)
- X. Other (please specify)

[Answer]: A (2026-09-25T14:33:12Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q2. How fine-grained should the units be?

Each unit passes through the design and build stages on its own. Smaller
units give more checkpoints, but more passes.

- A. About 9 units: repo foundation, API contract, analysis core (engine + scorer), guarded services (explanation + usage guard), backend service, two frontend units, curation tool, evaluation tool
- B. About 5 units: foundation + contract, analysis core, backend service (including explanation and the usage guard), frontend, author tools (curation + evaluation)
- C. Finer than A: a separate unit per component (about 12)
- X. Other (please specify)

[Answer]: A (2026-09-25T14:33:12Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q3. How is the frontend split?

The frontend carries about 17 of the 36 stories, which is too big for one
comfortable unit.

- A. Two UI units: "explore" (Browser, chart views, About & accuracy) and "investigate" (hypothesis panel, evidence, trace, review actions, ReviewStore)
- B. One UI unit
- X. Other (please specify)

[Answer]: A (2026-09-25T14:33:12Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q4. May independent units be built in parallel?

The dependency map will show which units do not depend on each other.
Delivery Planning still decides the actual order.

- A. Yes: independent units may run in parallel where the dependency map allows
- B. No: strictly one unit at a time
- X. Other (please specify)

[Answer]: A (2026-09-25T14:33:12Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q5. How do the units connect to each other?

These are the integration points between units. The architecture review of
Domain Design flagged that the evaluation report hand-off was unstated (R-01).

- A. Browser to backend: HTTP calls using the shared API contract. Backend to analysis core: in-process library calls. Curation and evaluation tools: import the analysis core directly and write versioned data files (catalog, pre-computed results, evaluation report) that the backend reads at startup
- B. The offline tools call the running backend over HTTP instead of writing files
- X. Other (please specify)

[Answer]: A (2026-09-25T14:33:12Z, **Mode:** chat — user accepted the recommended option: "all good")

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Backend: one deployable modular-monolith service (WebApi, CatalogService, RunService, UsageGuard, ExplanationService); the engine and scorer are an in-process library (Q1: A)
- 9 units: U1 repo foundation (packaging), U2 API contract (spec), U3 analysis core (library), U4 guarded services (library), U5 backend service (service), U6 web explore (ui), U7 web investigate (ui), U8 curation tool (packaging), U9 evaluation tool (library) (Q2: A)
- The frontend is split into "explore" and "investigate + review" units (Q3: A)
- Independent units may be built in parallel where the dependency map allows; Delivery Planning picks the order (Q4: A)
- Integration: HTTP with the shared contract between browser and backend; in-process calls to the analysis core; offline tools write versioned data files (catalog, pre-computed results, evaluation report) that the backend reads at startup (Q5: A)

Does this all look correct before I generate the units of work?

- Looks correct
- Request changes

[Answer]: Looks correct
