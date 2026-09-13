# Constraint Register — OrbitSleuth

## Technical Constraints

| ID | Constraint | Source |
|----|-----------|--------|
| TC-1 | No existing systems to integrate with (greenfield) | [Q1] |
| TC-2 | Analysis backend must be Python | [desc] |
| TC-3 | Evaluation must be measurable against a real labeled dataset — the NASA Exoplanet Archive's Cumulative KOI table (CONFIRMED/CANDIDATE/FALSE POSITIVE) — not synthetic data alone | [Q7] |
| TC-4 | AI analysis must be tool-backed (real signal-processing/astronomy tools the AI calls and interprets), not free-form LLM reasoning | [intent-statement] |
| TC-5 | Every AI hypothesis must carry cited evidence and a confidence/uncertainty level; no bare verdicts | [intent-statement] |
| TC-6 | Every AI hypothesis requires explicit human accept/reject/annotate before being treated as final; the human can also intervene at any point | [intent-statement] |

## Organizational Constraints

| ID | Constraint | Source |
|----|-----------|--------|
| OC-1 | Solo builder — no team to parallelize work across | [Q5], [intent-statement] |
| OC-2 | Available time may vary due to other commitments; no fixed deadline | [Q4], [Q5] |
| OC-3 | Decisions are made by the author alone | [intent-statement] |

## Regulatory / Compliance Constraints

| ID | Constraint | Source |
|----|-----------|--------|
| RC-1 | None identified for the MVP — public astronomical archive data and synthetic data only; no user accounts, payments, or personal/health data planned | [Q2] |

## Budget / Infrastructure Constraints

| ID | Constraint | Source |
|----|-----------|--------|
| BC-1 | Minimize cost — prefer free-tier/low-cost services throughout, including the eventual AWS deployment | [Q4] |
| BC-2 | An existing personal AWS account is available for the future deployment path, but no cloud resources are to be provisioned until the Unit of Work is approved | [Q6], [desc] |

## Sources

[desc], [intent-statement], [Q1]-[Q7] per `feasibility-questions.md`.

## Assumptions & Open Questions

None.
