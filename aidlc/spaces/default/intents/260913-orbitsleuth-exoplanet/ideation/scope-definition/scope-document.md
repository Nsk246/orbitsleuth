# Scope Document — OrbitSleuth

## Minimum Viable Scope

The MVP is one complete, evidence-linked, human-controlled investigation
loop [Q1]:

**Browse/select a light curve → run AI-backed analysis → see a ranked
hypothesis (transit / stellar activity / noise) with cited evidence and a
confidence score → human accepts, rejects, annotates, or requests more
evidence.**

## In Scope (Must Have)

| Capability | Rationale | Source |
|------------|-----------|--------|
| Interactive light-curve viewer (zoom/pan, raw vs. detrended) | Core to "explore telescope light curves" | [Q2], [desc] |
| On-demand AI-backed analysis with cited evidence + confidence score | Core evidence-linked-hypothesis commitment | [Q2], [intent-statement] |
| Human review UI: accept / reject / annotate, re-run, request more evidence | Core human-in-control commitment | [Q2], [intent-statement] |
| Basic observability: a visible trace of which analysis tools ran and what they returned, for the reviewer | Confirmed "lightweight but real" observability | [Q2], [Q7] |
| Evaluation methodology measuring hypothesis accuracy against the NASA Exoplanet Archive's Cumulative KOI table | Confirmed success metric (measurable analytical rigor); not necessarily its own dedicated UI screen — see Out of Scope note | [feasibility-assessment], [Q2] |

## Out of Scope (Won't Have, this MVP)

| Capability | Source |
|------------|--------|
| User accounts / authentication / saved personal investigation history | [constraint-register] (no accounts planned for MVP) |
| Multi-user collaboration or sharing features | [Q3] |
| Production-grade AWS deployment (staging/prod environments, CI/CD, autoscaling) | [Q3], [desc] (deployment plan waits for Unit of Work approval) |
| Exporting formal reports (PDF/CSV) | [Q3] |
| A dedicated "evaluation view" UI screen | [Q2] — the evaluation methodology itself is in scope (see In Scope), but a separate user-facing screen for it was not selected as a Must Have; evaluation results may surface elsewhere (e.g. alongside the observability trace) rather than as a standalone screen |

## Sequencing & Dependencies

- **Sequencing preference**: value-first — prioritize the most demoable, visually impressive parts of the workflow [Q4].
- **Confirmed dependency**: the analysis backend's evidence + confidence output shape must be defined before the frontend's hypothesis-display design, since the display depends on knowing that shape [Q5].
- **No hard deadline** on any capability [Q6].

## Value Stream

```
Visitor
  -> selects a light curve (real archival or synthetic)
  -> requests analysis
  -> AI calls tool-backed signal-processing analysis (period-detection, detrending, vetting)
  -> AI returns a ranked hypothesis with cited evidence + confidence
  -> Human reviewer inspects the trace, accepts/rejects/annotates, or requests more evidence
  -> (background) evaluation methodology tracks accuracy against the NASA KOI labeled table
```

## Sources

[desc], [intent-statement], [feasibility-assessment], [constraint-register], [Q1]-[Q7] per `scope-definition-questions.md`.

## Assumptions & Open Questions

None.
