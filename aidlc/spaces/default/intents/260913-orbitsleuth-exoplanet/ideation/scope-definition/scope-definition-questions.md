# Scope Definition & Prioritization — Questions

## Sources

- [desc] Initial description: "Plan OrbitSleuth, a resume-ready interactive AI exoplanet investigation platform. Users explore telescope light curves, investigate unusual signals with tool-backed AI analysis, and receive evidence-linked hypotheses such as exoplanet transit, stellar activity, or noise. The product must never claim a discovery without supporting evidence and must keep a human reviewer in control. Begin with AI-DLC discovery and planning only: do not generate application code, provision cloud resources, or create a deployment plan until I approve the Unit of Work. The target outcome is a visually compelling web experience with a Python analysis backend, an interactive frontend, evaluation, observability, and a future AWS deployment path."
- [intent-statement] `ideation/intent-capture/intent-statement.md`: solo-founder resume project; success = polish + measurable analytical rigor together; evidence-linked hypotheses with confidence levels; human accept/reject/annotate + intervene-anytime; tool-backed analysis; real (NASA KOI) + synthetic data.
- [feasibility-assessment] `ideation/feasibility/feasibility-assessment.md`: no regulatory constraints; cost-minimized, no hard deadline; skill fit confirmed; evaluation dataset = NASA Exoplanet Archive Cumulative KOI table.
- [constraint-register] `ideation/feasibility/constraint-register.md`: solo builder, Python analysis backend, tool-backed AI, evidence + confidence required, human-in-the-loop required.

## Q1. What is the minimum viable investigation workflow — the smallest end-to-end path a visitor must be able to complete?

- A. Browse/select a light curve → run the AI-backed analysis on it → see the ranked hypothesis (transit/stellar activity/noise) with cited evidence and confidence → accept/reject/annotate it
- B. Just a static gallery of pre-analyzed light curves with explanations (no live/on-demand analysis)
- C. A different minimum path (please specify in Other)
- D. Not yet defined
- [Answer]: A

## Q2. Which of these capabilities are Must Have for the MVP (select all that apply)?

- A. Interactive light-curve viewer (zoom/pan, view raw vs. detrended)
- B. On-demand AI-backed analysis of a selected signal with cited evidence and a confidence score
- C. Human review UI: accept / reject / annotate a hypothesis, with ability to re-run or request more evidence
- D. An evaluation view showing the AI's accuracy against the NASA KOI labeled dataset
- E. Basic observability (e.g., a simple log/trace of what the AI tool-chain did for a given analysis, visible to the reviewer)
- [Answer]: A, B, C, E

## Q3. Which of these are explicitly Won't Have for this MVP (out of scope for now) (select all that apply)?

- A. User accounts / authentication / saved personal investigation history
- B. Multi-user collaboration or sharing features
- C. Production-grade AWS deployment (staging/prod environments, CI/CD, autoscaling)
- D. Exporting formal reports (PDF/CSV) of findings
- E. None of these — they should all be considered in scope
- [Answer]: B, C, D

## Q4. What is the sequencing preference for building this out?

- A. Walking-skeleton-first — get one light curve flowing end-to-end (view → analyze → hypothesis → human review) before adding breadth (more signal types, nicer UI, more evaluation depth)
- B. Value-first — prioritize the most visually impressive/demoable parts first
- C. Risk-first — prioritize the least-certain technical piece first (e.g., the AI/tool-backed analysis accuracy) before UI polish
- D. Not yet defined
- [Answer]: B. Value-first

## Q5. Are there dependencies between capabilities that should drive build order (e.g., the evidence/confidence display design might depend on what the analysis tools actually output)?

- A. Yes — the analysis backend's output shape (evidence + confidence) should be defined before the frontend's hypothesis display is designed
- B. Yes — a different dependency matters more (please specify in Other)
- C. No strong dependency — these can be worked in parallel/either order
- D. Not yet defined
- [Answer]: A

## Q6. Are there hard deadlines tied to specific capabilities (e.g., needing a demoable version by a certain date)?

- A. No hard deadline for any capability — consistent with the "no hard deadline" answer from Feasibility
- B. Yes, a specific capability has a deadline (please specify in Other)
- C. Not applicable
- [Answer]: A

## Q7. How much "observability" belongs in this MVP scope, given the confirmed need for evaluation + human-in-the-loop review?

- A. Lightweight but real: a visible trace of which analysis tools ran and what they returned for a given investigation, plus basic evaluation metrics (accuracy against the KOI labels) — no full production monitoring stack
- B. Full production-grade observability (centralized logging/metrics/tracing infrastructure) is in scope now
- C. Skip observability entirely for the MVP; add it later
- D. Not yet defined
- [Answer]: A

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Looks correct
- Request changes

[Answer]: Looks correct
