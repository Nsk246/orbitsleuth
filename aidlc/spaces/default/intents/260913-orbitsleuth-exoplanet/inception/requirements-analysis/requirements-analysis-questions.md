# Requirements Analysis — Clarifying Questions

## Sources

- Initial description: `project-description.json` [desc]
- `ideation/intent-capture/intent-statement.md` [intent-statement]
- `ideation/feasibility/feasibility-assessment.md`, `constraint-register.md` [feasibility]
- `ideation/scope-definition/scope-document.md`, `intent-backlog.md` [scope]
- `ideation/rough-mockups/user-flow.md`, `wireframes.md` [mockups]
- `inception/practices-discovery/team-practices.md` [practices]

Already settled upstream and not re-asked here: MVP boundary, the three hard
product rules (evidence + confidence, human accept/reject/annotate,
tool-backed analysis), no accounts, KOI-table evaluation, responsive layout,
WCAG 2.1 AA, testing posture. Choosing the real light-curve data source is
left to a later design stage, as you asked [Q11 intent-capture].

## Q1. Which hypothesis categories should the analysis rank?

The request names three categories: exoplanet transit, stellar activity, and
noise. The NASA KOI evaluation table labels many targets FALSE POSITIVE, and a
large share of those are eclipsing binary stars. An eclipsing binary looks like
a transit but is not one. This choice decides what the analysis must tell apart
and how accuracy is scored.

- A. Only the three named categories: transit, stellar activity, noise
- B. The three named categories plus "eclipsing binary" as a fourth category
- C. The three named categories, and eclipsing-binary signs appear only as evidence against "transit", not as a separate category
- X. Other (please specify)

[Answer]: B (2026-09-25T13:53:25Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q2. What role does a large language model (LLM) play in the analysis?

"Tool-backed AI analysis" can be built several ways, and cost and design
depend on which one. Every option still runs real signal-processing tools and
never lets free-form reasoning decide the result.

- A. An LLM agent picks which analysis tools to call, reads their output, and writes the ranked hypothesis with its evidence explanation
- B. A fixed, deterministic tool pipeline produces the scores and ranking; an LLM only writes the plain-language explanation from the tool outputs
- C. No LLM: a fixed tool pipeline plus a classical scoring model (rules or a small trained classifier) produces everything
- X. Other (please specify)

[Answer]: B (2026-09-25T13:53:25Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q3. How is the confidence value shown and defined?

Every hypothesis must carry a confidence or uncertainty level. The mockup
shows "Transit (72% conf.)". This decides whether that number must be checked
against real outcomes.

- A. A 0–100% score that must be calibrated: across the evaluation set, hypotheses scored around 70% should turn out correct about 70% of the time
- B. A 0–100% score used only for ranking, not claimed to be a calibrated probability, and labeled that way in the UI
- C. Qualitative bands only (High / Medium / Low), each with a defined rule
- X. Other (please specify)

[Answer]: B (2026-09-25T13:53:25Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q4. Where are the human reviewer's decisions (accept / reject / annotate) saved?

There are no user accounts in the MVP, but anyone on the public site can act
as a reviewer. This decides whether decisions survive a page reload and
whether one visitor sees another visitor's decisions.

- A. Only in the visitor's own browser (for example local storage); not shared, and lost if the visitor clears the browser
- B. On the server as one shared anonymous log that all visitors can see
- C. Only for the current page session; nothing is saved after the tab closes
- D. On the server, but each visitor sees only their own decisions, tracked by an anonymous browser ID
- X. Other (please specify)

[Answer]: A (2026-09-25T13:53:25Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q5. How many light curves does the browser offer?

This decides the data-ingestion design and whether analysis can run ahead of
time. It does not choose the data source itself.

- A. A small curated set (about 20–50 real and synthetic curves) prepared ahead of time
- B. A curated set, plus a search box that fetches any Kepler/TESS target on demand
- C. Any target on demand only, with no curated set
- X. Other (please specify)

[Answer]: A (2026-09-25T13:53:25Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q6. What accuracy target should the evaluation measure against the KOI table?

The success metric is "measurable analytical rigor". A requirement needs a
pass/fail threshold, so this decides the number the evaluation must meet or
report.

- A. No fixed pass mark for the MVP: report accuracy, precision, and recall per category on a fixed, frozen sample (for example 100–200 KOI targets) as a baseline
- B. A fixed target: at least 80% agreement with KOI CONFIRMED vs FALSE POSITIVE labels on the frozen sample
- C. A fixed target: at least 90% agreement on the frozen sample
- X. Other (please specify)

[Answer]: A — frozen sample of about 150 KOI targets (2026-09-25T13:53:25Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q7. How long can a visitor wait for an analysis result?

The mockups show a loading state with a live step-by-step trace. This sets the
performance requirement.

- A. Under 10 seconds for a curated curve (results may be pre-computed)
- B. Under 30 seconds, with the step-by-step trace shown live while it runs
- C. Up to about 2 minutes is acceptable, as long as the trace shows progress
- X. Other (please specify)

[Answer]: A — plus under 30 seconds for a fresh re-run (2026-09-25T13:53:25Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q8. How should the public site guard against misuse and runaway cost?

The site is public with no login, and cost must stay minimal (budget
constraint BC-1). Analysis runs, and any paid AI calls, cost money and
compute each time.

- A. Per-visitor rate limit on analysis runs (for example 10 per hour) plus a hard daily spending cap that switches to cached results when reached
- B. Serve only cached, pre-computed analyses to public visitors; live re-runs only in a local or owner-only mode
- C. No limits needed for the MVP
- X. Other (please specify)

[Answer]: A (2026-09-25T13:53:25Z, **Mode:** chat — user accepted the recommended option: "all good")

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Hypothesis categories: transit, stellar activity, noise, plus eclipsing binary as a fourth category (Q1: B)
- LLM role: a deterministic tool pipeline computes scores and ranking; the LLM only writes the plain-language explanation from tool outputs (Q2: B)
- Confidence: a 0–100% ranking score, labeled in the UI as not a calibrated probability; calibration is a later stretch goal (Q3: B)
- Reviewer decisions are saved only in the visitor's own browser (Q4: A)
- Catalog: a curated set of about 20–50 real and synthetic curves prepared ahead of time (Q5: A)
- Evaluation: no fixed pass mark for the MVP; report per-category accuracy, precision, and recall on a frozen sample of about 150 KOI targets as a baseline (Q6: A)
- Wait time: under 10 seconds for a curated curve (pre-computed, with trace replay); under 30 seconds for a fresh re-run (Q7: A)
- Cost guard: per-visitor rate limit on analysis runs plus a hard daily spending cap that falls back to cached results (Q8: A)
- No additional topics raised

Does this all look correct before I generate the requirements artifact?

- Looks correct
- Request changes

[Answer]: Looks correct
