# Requirements — OrbitSleuth

## Intent Analysis

OrbitSleuth is a portfolio project. It shows end-to-end, tool-backed AI
analysis applied to a real scientific domain: finding and vetting signals in
exoplanet light curves [intent-statement]. Success needs two things together
[intent-statement]:

- a polished, interactive, responsive web experience that a hiring manager
  can try in under 10 minutes;
- analytical rigor that can be measured against a real labeled reference set
  (the NASA Exoplanet Archive Cumulative KOI table) [feasibility].

Three product rules bind every requirement below. Each rule has its own
automated test [practices]:

- **TC-4**: analysis is tool-backed, never free-form reasoning.
- **TC-5**: every hypothesis cites evidence and states a confidence level.
- **TC-6**: a human accepts, rejects, or annotates a hypothesis before it
  counts as final.

**Request type**: new greenfield product. **Scope**: multi-component (Python
analysis engine, API, interactive frontend, evaluation harness).
**Complexity**: standard. **Depth**: Standard [scope].

## Functional Requirements

### FR1 — Light-curve catalog and browsing

Traces to: intent-backlog capability 1, user-flow "Browse and filter" [scope] [mockups].

- **FR1.1** The system shall offer a curated catalog of 20–50 light curves,
  prepared ahead of time, containing both real archival curves and
  synthetic injected-signal curves [Q5].
- **FR1.2** The browser view shall show each catalog entry as a card with its
  target identifier and a thumbnail of the curve [mockups].
- **FR1.3** The browser view shall filter the catalog by Real / Synthetic / All
  [mockups].
- **FR1.4** When the catalog fails to load, the browser view shall show an
  inline retry message with no raw error code [mockups].
- **FR1.5** Each catalog entry shall record its provenance: the archive and
  mission for a real curve, or the generator parameters (injected period,
  depth, noise level) for a synthetic curve [intent-statement].

### FR2 — Interactive light-curve viewer

Traces to: intent-backlog capability 4 [scope].

- **FR2.1** The workspace shall plot the selected light curve (flux against
  time) with zoom and pan [scope].
- **FR2.2** The viewer shall toggle between raw and detrended views of the
  same curve [scope].
- **FR2.3** When analysis finds a candidate period, the viewer shall be able
  to show the curve phase-folded at that period, with the detected event
  marked [scope] (supports evidence review in FR4).
- **FR2.4** When a curve fails to load, the viewer shall show "This light
  curve could not be loaded" and a "Back to Browser" action [mockups].

### FR3 — Tool-backed analysis pipeline

Traces to: intent-backlog capability 2; TC-4 [scope] [feasibility].

- **FR3.1** The analysis engine shall run a fixed, deterministic pipeline of
  signal-processing tools: detrending, period detection (box-least-squares
  style transit search), and vetting statistics (at minimum: transit depth,
  duration, signal-to-noise, odd/even depth comparison, secondary-eclipse
  check) [intent-statement] [Q2].
- **FR3.2** The same input curve and pipeline version shall always produce the
  same scores and ranking [Q2].
- **FR3.3** Every analysis run shall have a recorded pipeline version and
  input identifier, so a result can be reproduced [Q2].
- **FR3.4** When a vetting check cannot run (for example, too few transits),
  the result shall mark that check explicitly as "not run", with a reason.
  The check shall never be silently dropped, and confidence shall reflect the
  gap [mockups].
- **FR3.5** When a pipeline step fails, the engine shall raise a typed error.
  The API shall return a structured "analysis failed" response that is
  distinct from a valid low-confidence result [practices].

### FR4 — Evidence-linked hypothesis generation

Traces to: intent-backlog capability 3; TC-5 [scope] [feasibility].

- **FR4.1** Each analysis shall return a ranked list across four categories:
  `transit`, `eclipsing_binary`, `stellar_activity`, `noise` [Q1].
- **FR4.2** Each ranked hypothesis shall carry a confidence score from 0 to
  100% and a non-empty list of evidence items. A hypothesis with no evidence
  or no confidence shall be rejected by the output contract [intent-statement] [practices].
- **FR4.3** Each evidence item shall name the tool that produced it, the
  measured value, and how that value supports or weakens the hypothesis
  [intent-statement].
- **FR4.4** Every evidence value shall trace to a recorded tool output from
  that run. No evidence value may come from LLM text alone (TC-4)
  [practices].
- **FR4.5** An LLM may write a plain-language explanation of the ranked
  result. The explanation shall only restate values from the tool outputs.
  The LLM shall not change a score, a ranking, or an evidence value [Q2].
- **FR4.6** When the explanation service is unavailable, the system shall
  still show the ranked hypotheses and evidence, with the explanation marked
  unavailable [Q2] [practices].
- **FR4.7** No output shall call a signal a "discovery" or a confirmed planet.
  Wording shall stay advisory, for example "consistent with a transit"
  [desc].
- **FR4.8** The shared hypothesis vocabulary (`transit`,
  `eclipsing_binary`, `stellar_activity`, `noise`, `confidence`, `evidence`)
  shall be identical in the engine, the API, and the frontend [practices].

### FR5 — Hypothesis display

Traces to: intent-backlog capability 5 [scope].

- **FR5.1** The workspace shall show the top hypothesis with its confidence
  score and evidence list, and the other ranked hypotheses beneath it
  [mockups].
- **FR5.2** The confidence score shall be labeled as a ranking score, not a
  calibrated probability [Q3].
- **FR5.3** The display shall show only values returned by the API. The
  frontend shall not recompute any score [practices].
- **FR5.4** Every hypothesis shall show its review status (unreviewed,
  accepted, rejected) with "unreviewed" as the default [intent-statement].

### FR6 — Human review workflow

Traces to: intent-backlog capability 6; TC-6 [scope] [feasibility].

- **FR6.1** The reviewer shall be able to accept or reject a hypothesis
  [intent-statement].
- **FR6.2** The reviewer shall be able to add an annotation (free text, up to
  2,000 characters) with or without accepting or rejecting [mockups].
- **FR6.3** A hypothesis shall become "final" only after a recorded human
  accept or reject action. No code path shall mark a hypothesis final
  without one (TC-6) [practices].
- **FR6.4** The reviewer shall be able to re-run analysis and to adjust named
  pipeline parameters before a re-run (at minimum: the detrending window and
  the period search range) [intent-statement].
- **FR6.5** The reviewer shall be able to request more evidence. The system
  shall then run additional vetting checks or an extended analysis and
  return to the loading state [mockups].
- **FR6.6** Review decisions and annotations shall persist in the visitor's
  own browser storage only. They shall survive a page reload in the same
  browser and are not shared with other visitors [Q4].
- **FR6.7** A re-run shall produce a new analysis result. It shall not change
  the stored review decision of an earlier result [intent-statement].

### FR7 — Analysis observability trace

Traces to: intent-backlog capability 7 [scope].

- **FR7.1** Each analysis run shall produce a trace listing every tool step in
  order, with its status, duration, key inputs, and key outputs [scope].
- **FR7.2** The workspace shall show the trace in an expandable panel.
  During a live run the panel shall update step by step. For a
  pre-computed result it shall replay the recorded steps [mockups] [Q7].
- **FR7.3** When a step fails, the trace shall mark which step failed and why
  [mockups].
- **FR7.4** When an LLM explanation is generated, the trace shall record that
  step and which tool outputs it used [Q2].

### FR8 — Evaluation against the KOI labeled table

Traces to: intent-backlog capability 8; TC-3 [scope] [feasibility].

- **FR8.1** The evaluation harness shall run the analysis pipeline on a
  frozen sample of about 150 KOI targets with known CONFIRMED and FALSE
  POSITIVE labels [Q6].
- **FR8.2** The frozen sample, including target IDs and labels, shall be
  versioned in the repository so that every evaluation run uses identical
  inputs [Q6] [practices].
- **FR8.3** The harness shall report accuracy, and per-category precision and
  recall, as a baseline. The MVP has no pass/fail threshold on these values
  [Q6].
- **FR8.4** The harness shall record the mapping from KOI labels to the four
  hypothesis categories, and report targets whose label does not map cleanly
  as a separate "unmapped" count rather than dropping them [Q1] [Q6].
- **FR8.5** The harness shall run separately from the pre-merge test suite, as
  an explicitly labeled validation run [practices].
- **FR8.6** The latest evaluation results shall be viewable somewhere in the
  product or repository (for example a report file or a section beside the
  trace). A dedicated evaluation screen is out of scope [scope].

### FR9 — Public-use guardrails

Traces to: budget constraint BC-1 [feasibility].

- **FR9.1** The system shall limit analysis runs per visitor (default: 10 per
  hour, configurable) [Q8].
- **FR9.2** The system shall enforce a hard daily spending cap on paid
  services (for example LLM calls), set in configuration [Q8].
- **FR9.3** When a limit or the cap is reached, the system shall serve cached
  results and tell the visitor in plain language that live runs are paused
  [Q8].

## Non-Functional Requirements

- **NFR1 — Performance**: For a curated curve, the result shall appear
  within 10 s of clicking "Analyze" (p95, pre-computed result with trace
  replay). A fresh re-run shall complete within 30 s (p95) for a curve of up
  to 70,000 data points [Q7].
- **NFR2 — Frontend responsiveness**: Zoom and pan on a 70,000-point curve
  shall respond within 100 ms per interaction on a mid-range laptop
  [intent-statement].
- **NFR3 — Accessibility**: All views shall meet WCAG 2.1 AA. Every action
  shall be keyboard-operable, and the chart region shall have a text summary
  [mockups].
- **NFR4 — Responsive layout**: The layout shall work at mobile (<768 px),
  tablet (768–1023 px), and desktop (≥1024 px) breakpoints [mockups].
- **NFR5 — Reproducibility**: Re-running the evaluation harness on the same
  frozen sample and pipeline version shall produce identical metrics
  [Q2] [Q6].
- **NFR6 — Reliability of error reporting**: No error in the ingestion,
  analysis, or API chain shall be silently swallowed. Every failure shall
  reach the user as a plain-language message and appear in the trace
  [practices].
- **NFR7 — Security**: No secrets in the repository; secrets come from
  environment variables. Secret scanning, dependency-vulnerability scanning,
  and Ruff `S` rules run before merge. All API inputs (target IDs,
  parameters, annotation text) are validated at the boundary [practices].
- **NFR8 — Cost**: Running costs shall stay within free-tier or low-cost
  services, bounded by the FR9 caps [feasibility].
- **NFR9 — Observability**: Each analysis run shall carry a run ID that links
  the API log entries and the trace. The service shall report at least one
  health signal and one error-rate count [scope].
- **NFR10 — Maintainability**: 80% line-coverage floor for both the Python
  and TypeScript code. Type hints on public analysis functions, TypeScript
  `strict` mode, and the analysis engine free of web-framework imports
  [practices].
- **NFR11 — Browser support**: The two latest major versions of Chrome,
  Firefox, Safari, and Edge [assumption].

## Constraints

- Python analysis backend (TC-2) [desc].
- Evaluation against the NASA KOI Cumulative table (TC-3) [feasibility].
- Tool-backed analysis (TC-4), evidence + confidence (TC-5), and human
  review before final (TC-6) [intent-statement].
- Solo builder; variable time; no fixed deadline (OC-1–OC-3) [feasibility].
- Minimize cost (BC-1). No cloud resources before Unit-of-Work approval
  (BC-2) [feasibility].
- No user accounts in the MVP [scope].

## Assumptions

- The real light-curve archive and access method are chosen in a later
  design stage [intent-statement]. FR1 depends only on a curated,
  pre-fetched set, so it holds for any chosen source.
- 70,000 points is used as the upper curve size in NFR1 and NFR2. It is
  about one Kepler long-cadence quarter series, and should be confirmed in
  design [assumption].
- NFR11 browser support is a reasonable portfolio default; nothing upstream
  set it [assumption].
- The explanation LLM provider is not yet chosen; FR4.5–FR4.6 hold for any
  provider [Q2].

## Out of Scope

- User accounts, authentication, and saved server-side history [scope].
- Multi-user collaboration or a shared review log [scope] [Q4].
- Production AWS deployment, environments, and deployment pipeline [scope].
- PDF/CSV report export [scope].
- A dedicated evaluation screen [scope].
- On-demand search of arbitrary Kepler/TESS targets [Q5].
- A calibrated-probability claim for the confidence score (possible later
  stretch goal) [Q3].

## Open Questions

- Which archive and access method supplies the real curves (for a design
  stage) [intent-statement].
- The exact KOI-label-to-category mapping, especially for FALSE POSITIVE
  sub-flags (for functional design) [Q1].
- The explanation LLM provider and its per-call cost, needed to set the FR9.2
  cap value (for NFR design).

## Sources

- [desc] Initial description, `project-description.json`
- [intent-statement] `ideation/intent-capture/intent-statement.md`
- [feasibility] `ideation/feasibility/feasibility-assessment.md`, `constraint-register.md`
- [scope] `ideation/scope-definition/scope-document.md`, `intent-backlog.md`
- [mockups] `ideation/rough-mockups/user-flow.md`, `wireframes.md`
- [practices] `inception/practices-discovery/team-practices.md`, `discovered-rules.md`
- [Q1]–[Q8] `inception/requirements-analysis/requirements-analysis-questions.md`
- [assumption] stated in this document, not yet confirmed

## Assumptions & Open Questions

- [assumption] 70,000-point upper curve size for NFR1/NFR2 — confirm in design.
- [assumption] NFR11 browser support set — no upstream source.
- Open: real-data archive selection; KOI-label-to-category mapping; LLM provider and cost cap value.
