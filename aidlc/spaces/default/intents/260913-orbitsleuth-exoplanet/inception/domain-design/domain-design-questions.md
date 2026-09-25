# Domain Design — Clarifying Questions

## Sources

- [requirements] `inception/requirements-analysis/requirements.md`
- [stories] `inception/user-stories/stories.md` (36 stories, groups 0–6)
- [mockups] `inception/refined-mockups/mockups.md`, `interaction-spec.md`
- [practices] `inception/practices-discovery/team-practices.md` (analysis engine is a plain Python package; the API is the only translation layer; the frontend never recomputes)

Already settled and not re-asked: the analysis engine stays free of web
frameworks, the API is the only translation point, review decisions live in
the visitor's browser, the scoring is deterministic and the LLM only explains,
and the real-data archive is chosen by the spike story US0.2. Deployment
topology (one service or several) is decided later, in Units Generation.

## Q1. Is hypothesis scoring its own building block, or part of the analysis engine?

Scoring turns vetting results into the four ranked hypotheses and enforces the
evidence-and-confidence output contract (US0.6). It will likely change more
often than the signal-processing tools, as thresholds get tuned against the
evaluation.

- A. Separate building block ("HypothesisScorer") that consumes engine tool outputs: scoring rules can change and be re-evaluated without touching the signal-processing code
- B. Part of one "AnalysisEngine" building block: fewer boundaries, but scoring changes and tool changes are mixed together
- X. Other (please specify)

[Answer]: A (2026-09-25T14:24:44Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q2. Which building block owns the "final" review status?

The user-stories review left this open (AC5.1.3). Decisions are stored only in
the visitor's browser (FR6.6), and the API must never emit a final status
(AC0.6.5).

- A. A frontend "ReviewStore" alone: the only code that can set accepted/rejected; the TC-6 integration test targets it
- B. A backend review endpoint validates each transition, with the result still stored in the browser: two places to keep in sync
- X. Other (please specify)

[Answer]: A (2026-09-25T14:24:44Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q3. Where do curation and evaluation live?

Curating the catalog, pre-computing results (US6.1–US6.3) and running the KOI
evaluation (US6.4–US6.5) are author-only jobs that reuse the analysis engine.

- A. Separate offline building blocks ("CurationPipeline", "EvaluationHarness") run from the command line; they call the engine directly and are never exposed to visitors
- B. Admin features inside the public backend service
- X. Other (please specify)

[Answer]: A (2026-09-25T14:24:44Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q4. How is the LLM explanation isolated?

The explanation runs after the result is frozen, is checked at runtime for
numbers and forbidden words, and must degrade to "unavailable" (US4.3, US4.4).

- A. Its own building block ("ExplanationService") behind a provider-neutral interface, including the runtime guard: the provider can be swapped or turned off without touching analysis code
- B. A step inside the run orchestration code
- X. Other (please specify)

[Answer]: A (2026-09-25T14:24:44Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q5. Who owns analysis runs, traces, and stored results?

A run has a run ID, a pipeline version, a trace, and a result. Both
pre-computed (curated) and live runs produce them (US3.1, US6.3, US6.7).

- A. One "RunService" owns every run record, trace, and stored result; pre-computation writes through it, so live and pre-computed runs share one shape and one lookup
- B. The catalog owns pre-computed results; RunService owns only live runs
- X. Other (please specify)

[Answer]: A (2026-09-25T14:24:44Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q6. Where do rate limiting and the cost meter live?

These are the per-visitor live-run limit, the daily spending cap, and the
per-call cost meter (US6.6).

- A. A separate "UsageGuard" building block that RunService and ExplanationService consult before live work
- B. Inside the API request handling
- X. Other (please specify)

[Answer]: A (2026-09-25T14:24:44Z, **Mode:** chat — user accepted the recommended option: "all good")

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- HypothesisScorer is a separate building block that consumes engine tool outputs (Q1: A)
- A frontend ReviewStore alone owns the final review status; the TC-6 test targets it (Q2: A)
- CurationPipeline and EvaluationHarness are offline command-line building blocks that call the engine directly (Q3: A)
- ExplanationService is its own building block behind a provider-neutral interface, including the runtime guard (Q4: A)
- One RunService owns every run record, trace, and stored result (Q5: A)
- A separate UsageGuard enforces rate limits, the spending cap, and the cost meter (Q6: A)

Does this all look correct before I generate the component catalogue?

- Looks correct
- Request changes

[Answer]: Looks correct
