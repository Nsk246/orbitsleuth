# RAID Log — OrbitSleuth

## Risks

| ID | Risk | Likelihood | Impact | Mitigation | Source |
|----|------|-----------|--------|------------|--------|
| R-1 | Solo-builder scope creep beyond the confirmed `mvp` boundary | Medium | Medium | Hold to the confirmed `mvp` scope; add polish only after the core evidence-linked investigation workflow works end-to-end | [Q8], [intent-statement] |
| R-2 | Frontend framework learning curve slows delivery given growing (not expert) skill | Medium | Low-Medium | Time availability already flagged as variable; no external deadline pressure | [Q3], [Q5] |
| R-3 | Real archival light-curve data quality issues (gaps, noise, instrument artifacts) complicate analysis | Medium | Medium | Detrending step in the analysis pipeline is designed specifically to address this; well-documented in the astronomy community | [feasibility-assessment] |
| R-4 | AI reasoning drifts toward asserting a hypothesis without adequate cited evidence | Low | High (violates a core product commitment) | Enforce evidence-linking and confidence levels as a hard design constraint from Domain Design onward, not an afterthought | [intent-statement] |

## Assumptions

| ID | Assumption | Status | Source |
|----|-----------|--------|--------|
| A-1 | The NASA Exoplanet Archive's Cumulative KOI table remains freely accessible and suitable for evaluation throughout the project | Unvalidated | [Q7] |
| A-2 | An existing personal AWS account (available per [Q6]) will remain usable and within Free Tier limits when the deployment stage is reached | Unvalidated | [Q6] |
| A-3 | The specific real archival data source(s) and access method beyond the KOI evaluation table will be identified during a later design stage | Unvalidated (carried forward) | [assumption], `ideation/intent-capture/intent-statement.md` |

## Issues

None identified at this stage.

## Dependencies

| ID | Dependency | Source |
|----|-----------|--------|
| D-1 | Continued public availability of NASA Exoplanet Archive data and APIs | [Q7] |
| D-2 | Availability of an existing personal AWS account for the eventual deployment path | [Q6] |

## Sources

[desc], [intent-statement], [Q1]-[Q7], `feasibility-assessment.md`.

## Assumptions & Open Questions

None.
