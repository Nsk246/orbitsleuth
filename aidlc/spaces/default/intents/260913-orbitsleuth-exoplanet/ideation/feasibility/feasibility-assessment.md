# Feasibility Assessment — OrbitSleuth

## Overview

This is a greenfield, solo-founder project [Q1]. The technical approach —
Python-based signal-processing analysis, an interactive modern frontend, an
AI layer that calls and interprets real vetting tools, and a public NASA
archival dataset with a known confirmed/false-positive label set for
evaluation — is well-precedented; none of it requires novel research. The
main feasibility risk is scope discipline for a solo builder, not technical
uncertainty in any single component.

## Technical Viability

- **Data access**: Public archival light-curve data (Kepler/TESS/K2-style)
  is freely available with no access barriers, and the evaluation dataset —
  the NASA Exoplanet Archive's Cumulative Kepler Objects of Interest (KOI)
  table, which labels each target CONFIRMED / CANDIDATE / FALSE POSITIVE —
  is queryable via web UI, bulk download, or the `astroquery` Python API
  [Q7]. This directly satisfies the confirmed requirement that hypothesis
  accuracy be measurable against a real, labeled reference set rather than
  synthetic data alone [intent-statement].
- **Analysis approach**: Classical, well-established astronomical
  signal-processing techniques (period-detection/box-least-squares-style
  transit search, detrending, vetting statistics) are mature, well-documented,
  and implementable in Python without novel algorithm development
  [intent-statement].
- **Skill fit**: The builder's stated skill profile (comfortable with Python
  for analysis, growing into a modern JS/TS frontend, limited/growing AWS
  experience) [Q3] is viable for this scope: the Python analysis layer plays
  to existing strength, the frontend is a stretch but a well-supported one
  given the breadth of modern framework tooling and documentation, and AWS
  is deferred to a later phase by explicit design [desc].
- **Evidence and human-control commitments are implementable now**: nothing
  about requiring cited evidence + confidence levels per hypothesis, or
  requiring human accept/reject/annotate action before finalizing a result,
  demands unusual infrastructure — these are application-logic and UX
  design decisions to make in Domain Design / Functional Design, not
  technology risks.

## Risk Analysis

| Risk | Likelihood | Impact | Notes |
|------|-----------|--------|-------|
| Solo-builder scope creep (adding polish/features beyond MVP) | Medium | Medium | Mitigated by the confirmed `mvp` scope boundary [Q8, intent-statement] |
| Frontend framework learning curve slows delivery | Medium | Low-Medium | Skill profile names this as growing, not absent [Q3]; time availability is already flagged as variable [Q5] |
| Real archival data has quality issues (gaps, noise, instrument artifacts) requiring extra preprocessing | Medium | Medium | Standard, well-documented characteristic of Kepler/TESS data; detrending step in the analysis pipeline exists specifically to address this |
| AWS deployment cost overrun | Low | Low | Explicitly deferred; Free Tier-oriented approach confirmed [Q4, Q6] |

## Sources

- [desc] Initial description
- [intent-statement] `ideation/intent-capture/intent-statement.md`
- [Q1]-[Q7] `feasibility-questions.md`

## Assumptions & Open Questions

None.
