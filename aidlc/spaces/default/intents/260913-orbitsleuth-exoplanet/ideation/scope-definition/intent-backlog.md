# Intent Backlog — OrbitSleuth

Prioritized using MoSCoW, per the confirmed must-have/won't-have scope
decisions [Q2], [Q3].

## Must Have (proto-Units)

| # | Capability | Depends on | Source |
|---|-----------|-----------|--------|
| 1 | Data ingestion: fetch/prepare real archival light curves (NASA-archive-style) and synthetic/injected-signal light curves | — | [intent-statement] |
| 2 | Tool-backed analysis engine (Python): period-detection/box-least-squares-style transit search, detrending, vetting statistics | Capability 1 | [intent-statement], [feasibility-assessment] |
| 3 | Evidence-linked hypothesis generation: rank transit / stellar activity / noise with cited evidence and a confidence score | Capability 2 (defines the output shape) | [Q5], [intent-statement] |
| 4 | Interactive light-curve viewer (zoom/pan, raw vs. detrended) | Capability 1 | [Q2] |
| 5 | Hypothesis display in the frontend, driven by Capability 3's output shape | Capability 3 (explicit dependency: output shape before display design) | [Q2], [Q5] |
| 6 | Human review workflow: accept / reject / annotate a hypothesis, re-run analysis, request more evidence | Capability 3, Capability 5 | [Q2], [intent-statement] |
| 7 | Basic observability trace: visible record of which analysis tools ran and what they returned, for the reviewer | Capability 2 | [Q2], [Q7] |
| 8 | Evaluation methodology: measure hypothesis accuracy against the NASA Exoplanet Archive's Cumulative KOI labeled table | Capability 3 | [feasibility-assessment] |

## Should Have

None confirmed beyond the Must Have set. The confirmed success criterion of
a "polished, interactive, modern, smooth and responsive" frontend
[intent-statement] is a quality attribute (NFR) that applies across the
Must Have capabilities above, not a separate backlog item — it will be
carried into NFR Requirements rather than listed as its own Unit here.

## Could Have

None identified at this stage.

## Won't Have (this MVP)

| Capability | Source |
|-----------|--------|
| User accounts / authentication / saved personal investigation history | [constraint-register] |
| Multi-user collaboration or sharing features | [Q3] |
| Production-grade AWS deployment (staging/prod, CI/CD, autoscaling) | [Q3], [desc] |
| Exporting formal reports (PDF/CSV) | [Q3] |
| A dedicated "evaluation view" UI screen (evaluation methodology itself is in scope; a standalone screen for it is not) | [Q2] |

## Sequencing Note

Value-first sequencing was confirmed [Q4], with one explicit ordering
constraint: Capability 3 (the analysis backend's evidence + confidence
output shape) must be defined before Capability 5 (the frontend's
hypothesis display) is designed [Q5]. Within that constraint, later Bolt
sequencing (Delivery Planning, stage 2.9) will choose the specific build
order to maximize early demoable value.

## Sources

[desc], [intent-statement], [feasibility-assessment], [constraint-register], [Q1]-[Q7] per `scope-definition-questions.md`.

## Assumptions & Open Questions

None.
