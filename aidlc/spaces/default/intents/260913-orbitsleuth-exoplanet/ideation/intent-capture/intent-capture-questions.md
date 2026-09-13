# Intent Capture & Framing — Questions

## Sources

- [desc] Initial description: "Plan OrbitSleuth, a resume-ready interactive AI exoplanet investigation platform. Users explore telescope light curves, investigate unusual signals with tool-backed AI analysis, and receive evidence-linked hypotheses such as exoplanet transit, stellar activity, or noise. The product must never claim a discovery without supporting evidence and must keep a human reviewer in control. Begin with AI-DLC discovery and planning only: do not generate application code, provision cloud resources, or create a deployment plan until I approve the Unit of Work. The target outcome is a visually compelling web experience with a Python analysis backend, an interactive frontend, evaluation, observability, and a future AWS deployment path."
- [scope] Workflow-selected scope: `mvp`.

## Q1. What business problem are we solving?

- A. Demonstrating end-to-end AI-assisted scientific investigation skill (a portfolio/resume project) rather than solving a production business need
- B. Helping amateur/citizen astronomers triage real telescope data faster
- C. Helping a research team pre-screen light curves before human review
- D. Not yet defined
- E. Not applicable
- [Answer]:A

## Q2. Who is the customer (internal/external)? What pain are they experiencing?

- A. The author themself, as a demonstration to hiring managers/recruiters of applied-AI + full-stack skill
- B. Citizen-science enthusiasts who want an approachable way to explore light curves
- C. A specific research group with a real backlog of unreviewed signals
- D. Not yet defined
- E. Not applicable
- [Answer]:A, and normal fellow visitors public online

## Q3. What does success look like? What metrics matter?

- A. A polished, demoable product (visual quality, smooth UX, clear AI reasoning trace) that showcases skill to reviewers/employers
- B. Analytical accuracy against a labeled dataset (e.g., precision/recall on known exoplanet transits)
- C. Both — a demoable product AND measurable analytical rigor with evaluation metrics
- D. Not yet defined
- E. Not applicable
- [Answer]:C, and a really interactive and modern front end, smooth and responsive

## Q4. What is the trigger for this initiative (market pressure, tech debt, regulation, opportunity)?

- A. Career/opportunity — building a standout AI-era resume project
- B. Personal interest in astronomy/citizen science
- C. Responding to an existing team or community need
- D. Not yet defined
- E. Not applicable
- [Answer]: A

## Q5. Who are the key stakeholders and what does each care about?

- A. Just the author/builder (sole stakeholder) — cares about demonstrating skill and shipping something complete
- B. The author plus prospective employers/reviewers who will evaluate the project — care about code quality, AI-safety rigor, and polish
- C. The author plus a real user community (citizen scientists / researchers) — care about analytical usefulness
- D. Not yet defined
- E. Not applicable
- [Answer]: A

## Q6. Who decides scope or priority, and who influences those decisions?

- A. The author decides alone
- B. The author decides, informed by what best demonstrates skill to a target audience (e.g., specific job roles)
- C. Not yet defined
- D. Not applicable
- [Answer]: A

## Q7. Are there communication requirements or a reporting cadence?

- A. None — this is a solo project with no external reporting cadence
- B. Periodic self-review checkpoints only (no external audience during build)
- C. Not yet defined
- D. Not applicable
- [Answer]: A

## Q8. The workflow was started with the scope in `[scope]`; does that scope match the user's intended product boundary?

- A. Yes, `mvp` (Standard depth, skip full Operations) matches — I want the core interactive investigation experience, evaluation, and observability, without full production/AWS build-out yet
- B. No — I want a lighter scope (skip evaluation/observability details for now)
- C. No — I want a heavier scope (e.g., full enterprise-grade planning including deployment)
- D. Not yet defined
- E. Not applicable
- [Answer]:A

## Q9. What does "never claim a discovery without supporting evidence" mean operationally for the AI's output?

- A. Every hypothesis the AI proposes (transit / stellar activity / noise / other) must cite the specific evidence (e.g., transit depth, periodicity, odd/even mismatch, vetting-tool output) that supports it, and must state a confidence/uncertainty level rather than a bare verdict
- B. The AI's role is strictly advisory — it proposes ranked hypotheses with evidence, but only a human reviewer can mark a signal as a "confirmed candidate"
- C. Both A and B together
- D. Not yet defined
- E. Not applicable
- [Answer]:A

## Q10. What does "keep a human reviewer in control" mean for the workflow?

- A. Every AI-generated hypothesis requires an explicit human accept/reject/annotate action before it is considered final in the tool
- B. The human can intervene at any step (re-run analysis, adjust parameters, request more evidence) rather than only approving/rejecting a final verdict
- C. Both A and B
- D. Not yet defined
- E. Not applicable
- [Answer]:C

## Q11. What real or reference data will the platform use for light curves?

- A. Public archival data (e.g., NASA Kepler/TESS/K2 light curve archives) accessed via known public astronomy data sources
- B. Synthetic/simulated light curves generated for demo purposes
- C. Both real archival data and synthetic data (synthetic for injected-signal testing/evaluation, real for exploration)
- D. Not yet defined
- E. Not applicable
- [Answer]:C, you find relevant data

## Q12. What should the "tool-backed AI analysis" actually invoke — i.e., what kinds of tools/algorithms back the AI's investigation?

- A. Classical signal-processing/astro tools (e.g., period-detection/box-least-squares-style transit search, detrending, vetting statistics) that the AI calls and interprets, not free-form LLM guessing
- B. An LLM reasoning purely over pre-computed features/summaries without invoking analysis tools itself
- C. Not yet defined
- D. Not applicable
- [Answer]:A

## Assumptions & Open Questions

None.

## Assumption Confirmation

The following assumption was recorded in `intent-statement.md` because it
could not be confirmed as a settled decision:

- The exact public archival data source(s) and access method for real light
  curves are not yet selected; the user asked that this be identified
  during a later design stage rather than fixed now [Q11] [assumption].

A. Accept assumptions
B. Convert to follow-up questions

[Answer]: A. Accept assumptions

## Consolidated Summary Confirmation

- Looks correct
- Request changes

[Answer]: Looks correct
