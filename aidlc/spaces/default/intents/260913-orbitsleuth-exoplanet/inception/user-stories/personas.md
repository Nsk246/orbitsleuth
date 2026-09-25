# Personas — OrbitSleuth

## P1 — Riya the Recruiter (primary)

- **Role**: hiring manager or technical recruiter reviewing a candidate's
  portfolio [intent-statement].
- **Goals**: see in under 10 minutes that the candidate can build a polished,
  rigorous AI product; understand what the AI did and why.
- **Pain points**: portfolio demos that are slow, broken, or hide how the AI
  reaches its answer; "AI magic" with no evidence.
- **Context**: one short visit, usually on a laptop, sometimes on a phone
  from a link in a resume. No astronomy background assumed.
- **Tech comfort**: medium to high.
- **Frequency**: once or twice.

## P2 — Casey the Curious Visitor (secondary)

- **Role**: member of the public who finds the site online [intent-statement].
- **Goals**: explore real and synthetic light curves, try the analysis, and
  make their own call on a signal.
- **Pain points**: jargon without explanation; results presented as fact.
- **Context**: longer, exploratory sessions; may come back later in the same
  browser.
- **Tech comfort**: low to medium.
- **Frequency**: occasional, possibly repeat.

## P3 — Alex the Author/Operator

- **Role**: the solo builder who curates the catalog, runs the evaluation,
  and keeps costs bounded [constraint-register OC-1, OC-3].
- **Goals**: prove measurable accuracy against the KOI table; keep running
  costs near zero; reproduce any result on demand.
- **Pain points**: unexplained cost spikes; results that cannot be
  reproduced; silent failures.
- **Context**: works from the repository and configuration, not the public UI.
- **Tech comfort**: high.
- **Frequency**: during development and before each demo.

## Relationships and Priority

1. **P1 Recruiter**: the project exists to impress this audience. The
   journey must work fast and look credible on the first visit.
2. **P2 Curious Visitor**: uses the same journey as P1, but at a slower pace
   and deeper. Every P1 story also serves P2.
3. **P3 Author/Operator**: has no public UI. Owns the evaluation, the
   curation, and the guardrails that make P1's and P2's experience
   trustworthy and affordable.

In stories, "visitor" means P1 or P2 when both apply.

## Sources

- [intent-statement] `ideation/intent-capture/intent-statement.md`
- [constraint-register] `ideation/feasibility/constraint-register.md`
- [Q1] `inception/user-stories/user-stories-questions.md`

## Assumptions & Open Questions

None.
