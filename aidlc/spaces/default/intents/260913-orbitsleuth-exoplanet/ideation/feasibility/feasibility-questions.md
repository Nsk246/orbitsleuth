# Feasibility & Constraints — Questions

## Sources

- [desc] Initial description: "Plan OrbitSleuth, a resume-ready interactive AI exoplanet investigation platform. Users explore telescope light curves, investigate unusual signals with tool-backed AI analysis, and receive evidence-linked hypotheses such as exoplanet transit, stellar activity, or noise. The product must never claim a discovery without supporting evidence and must keep a human reviewer in control. Begin with AI-DLC discovery and planning only: do not generate application code, provision cloud resources, or create a deployment plan until I approve the Unit of Work. The target outcome is a visually compelling web experience with a Python analysis backend, an interactive frontend, evaluation, observability, and a future AWS deployment path."
- [intent-statement] `ideation/intent-capture/intent-statement.md`: solo-founder resume project; both real archival and synthetic light-curve data; tool-backed analysis (period-detection/detrending/vetting); evidence-linked hypotheses with confidence levels; human-in-control at every step.

## Q1. What existing systems must this integrate with?

- A. None — this is a greenfield project with no existing systems to integrate with
- B. It should integrate with an existing personal project/codebase (please specify in Other)
- C. Not yet defined
- D. Not applicable
- [Answer]: A — 2026-09-13T20:55:57Z **Mode:** chat

## Q2. Are there regulatory or compliance requirements (e.g. data privacy, PCI, HIPAA, SOC2, data residency)?

- A. None expected — the platform uses public astronomical archive data and synthetic data, with no user accounts, payments, or personal/health data planned for the MVP
- B. Basic user-account/authentication data will be collected, so standard privacy practices (not a formal compliance framework) should apply
- C. A specific regulatory framework applies (please specify in Other)
- D. Not yet defined
- E. Not applicable
- [Answer]: A — 2026-09-13T20:55:57Z **Mode:** chat

## Q3. What is the team's current tech stack and skill profile?

- A. Comfortable with Python for data/analysis work and reasonably comfortable picking up a modern JS/TS frontend framework; AWS experience is limited/growing
- B. Strong across the full stack (Python, frontend framework, AWS) already
- C. Primarily one side (either Python/data or frontend) with the other side newer territory (please specify which in Other)
- D. Not yet defined
- [Answer]: A — 2026-09-13T20:55:57Z **Mode:** chat

## Q4. What are the budget and timeline constraints?

- A. Minimize cost — prefer free-tier/low-cost services throughout (including for the eventual AWS deployment), with no hard deadline
- B. Minimize cost, but with a target completion date in mind (please specify in Other)
- C. Budget is flexible; timeline is the binding constraint (please specify target date in Other)
- D. Not yet defined
- E. Not applicable
- [Answer]: A — 2026-09-13T20:55:57Z **Mode:** chat

## Q5. Are there organizational blockers (change freeze, competing priorities, limited availability)?

- A. None — this is a solo side project with no organizational constraints, though available time may be limited by other commitments
- B. Yes, a specific blocker applies (please specify in Other)
- C. Not applicable
- [Answer]: A — 2026-09-13T20:55:57Z **Mode:** chat

## Q6. What AWS services and accounts are currently in use, or available for the future deployment path?

- A. No AWS account/services yet — a new personal AWS account (likely within Free Tier limits) would be created when the deployment stage is reached
- B. An existing personal AWS account is already available and could be used
- C. Not yet defined
- D. Not applicable
- [Answer]: B. The NASA Exoplanet Archive's Cumulative Kepler Objects of Interest (KOI) table (per-target `koi_disposition`: CONFIRMED / CANDIDATE / FALSE POSITIVE), freely queryable via web UI, bulk download, or the `astroquery` Python API. — 2026-09-13T20:58:28Z **Mode:** chat

## Q7. Given the requirement to never claim a discovery without evidence, is there a known reference/labeled dataset the evaluation stage should measure hypothesis accuracy against?

- A. No specific dataset chosen yet — identify a suitable public labeled dataset (e.g. a known set of confirmed/false-positive transit signals) during Domain Design or NFR Requirements
- B. A specific dataset should be used (please specify in Other)
- C. Not applicable — evaluation should rely on synthetic/injected signals only, not a labeled real dataset
- D. Not yet defined
- [Answer]: A — 2026-09-13T20:55:57Z **Mode:** chat

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Looks correct
- Request changes

[Answer]: Looks correct
