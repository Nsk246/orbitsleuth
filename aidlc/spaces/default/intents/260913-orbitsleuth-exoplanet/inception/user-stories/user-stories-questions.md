# User Stories — Story Plan and Questions

## Sources

- [requirements] `inception/requirements-analysis/requirements.md` (FR1–FR9, NFR1–NFR11)
- [intent-statement] `ideation/intent-capture/intent-statement.md`
- [mockups] `ideation/rough-mockups/user-flow.md`, `wireframes.md`
- [practices] `inception/practices-discovery/team-practices.md`

## Story Plan

- **Persona approach**: define personas from the confirmed audiences:
  hiring managers/recruiters, curious public visitors, and the author
  [intent-statement]. Q1 sets the final list.
- **Story format**: "As a [persona], I want [goal], so that [benefit]",
  checked against INVEST. Acceptance criteria use Given/When/Then with IDs
  `AC{group}.{seq}.{n}` [inception rules].
- **Prioritization**: MoSCoW per story, from the requirements. Delivery
  Planning sets the final MVP boundary.
- **Breakdown approach**: Q2 sets it.
- **Granularity**: Q3 sets it.
- **Non-functional requirements** (performance, accessibility, security,
  and similar): carried as acceptance criteria on the stories they touch.
  NFRs with no user-visible behavior are marked "Deferred" to NFR
  Requirements in traceability.

## Q1. Which personas should the stories be written for?

The intent names hiring managers/recruiters as the main audience and casual
public visitors as a second one. The author also runs the evaluation and sets
the cost caps. Stories for evaluation (FR8) and cost limits (FR9) need an
actor.

- A. Three personas: Recruiter/hiring manager (short demo visit), Curious public visitor (explores freely), and the Author/operator (runs evaluation, configures caps)
- B. Two personas: one merged "Visitor" (recruiter and public) plus the Author/operator
- C. Only the two visitor personas; evaluation and caps become technical tasks, not stories
- X. Other (please specify)

[Answer]: A (2026-09-25T13:59:08Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q2. How should stories be grouped?

Grouping decides how the story list reads and how Units of Work are cut
later.

- A. By step in the investigation journey: browse, view curve, analyze, review hypothesis, act on it, plus an operator group
- B. By requirement area (catalog, viewer, analysis, hypothesis, review, trace, evaluation, guardrails), one group per requirement group FR1–FR9
- C. By persona
- X. Other (please specify)

[Answer]: A (2026-09-25T13:59:08Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q3. How large should each story be?

You build alone with variable time [constraint-register OC-1, OC-2]. Smaller
stories mean more demoable checkpoints but a longer list.

- A. Small: each story is about 1–3 days of work, about 20–30 stories in total
- B. Medium: each story is about 3–5 days of work, about 12–18 stories in total
- X. Other (please specify)

[Answer]: A (2026-09-25T13:59:08Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q4. How should failure and edge cases appear?

The requirements define a load failure, an analysis failure, a partial
result (a vetting check that could not run), and a paused state when a limit
is reached (FR1.4, FR2.4, FR3.4, FR3.5, FR9.3).

- A. As acceptance criteria inside the happy-path story they belong to
- B. As separate stories, so each failure path can be scheduled and tested on its own
- X. Other (please specify)

[Answer]: A — plus one dedicated story for the "analysis failed" vs "low-confidence result" distinction (2026-09-25T13:59:08Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q5. The engine and setup work is bigger than it looked. Allow a longer story list?

The developer found two stories hiding far more work than 1–3 days. "Get a fast analysis result" (US3.1) contains the whole analysis engine. "Curate the catalog" (US6.1) contains the fetch and pre-compute pipeline. There are also four pieces of setup work that no story covers: repository and CI setup, the shared API contract, the catalog and curve endpoints, and a one-day spike to choose the data source. Splitting these out keeps each story at 1–3 days, but the list grows to about 34 stories, above the 20–30 you chose in Q3.

- A. Split them out as separate stories owned by the Author/operator (about 34 small stories)
- B. Keep about 26 stories and accept that a few are 1–2 weeks of work each
- X. Other (please specify)

[Answer]: A (2026-09-25T14:05:36Z, **Mode:** guided — mob judgment call)

## Q6. Add a "featured example" starting point for recruiters?

No story currently tests the recruiter's under-10-minute visit. The designer proposes a new Should Have story: a one-line description of OrbitSleuth and a "Start with a featured example" button above the catalog. It would reach a reviewable result in 3 clicks or fewer.

- A. Yes, add it as a Should Have story
- B. No, the catalog alone is enough
- X. Other (please specify)

[Answer]: A (2026-09-25T14:05:36Z, **Mode:** guided — mob judgment call)

## Q7. Can a visitor change an accept/reject decision after making it?

The testers need this settled to pin down the rule that nothing is final without a human decision.

- A. Yes: the visitor can change or clear the decision, and the latest human action sets the status
- B. No: once accepted or rejected, the decision is locked for that run (a re-run gives a fresh result to review)
- X. Other (please specify)

[Answer]: A (2026-09-25T14:05:36Z, **Mode:** guided — mob judgment call)

## Q8. What exactly does "Request more evidence" run?

As written, it runs "additional checks or an extended analysis". That cannot be built or tested until the checks are named.

- A. Name the checks now: a harmonic check at half and double the detected period, plus a period search over a wider range. Keep the story as Should Have
- B. Lower it to Could Have, and name the checks in functional design
- X. Other (please specify)

[Answer]: A (2026-09-25T14:05:36Z, **Mode:** guided — mob judgment call)

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Three personas: Recruiter/hiring manager, Curious public visitor, Author/operator (Q1: A)
- Stories grouped by investigation-journey step, plus an operator group (Q2: A)
- Small stories of about 1–3 days each, about 20–30 in total (Q3: A)
- Failure and edge cases are acceptance criteria inside their happy-path story, except one dedicated story for "analysis failed" vs "low-confidence result" (Q4: A)
- Engine, pipeline, and setup work split into separate Author/operator stories, so the list grows to about 34 small stories (Q5: A)
- New Should Have story: a featured example that reaches a reviewable result in 3 clicks or fewer (Q6: A)
- A visitor can change or clear an accept/reject decision; the latest human action sets the status (Q7: A)
- "Request more evidence" runs a harmonic check at half and double the detected period plus a wider period search; stays Should Have (Q8: A)

Does this all look correct before I generate the stories?

- Looks correct
- Request changes

[Answer]: Looks correct
