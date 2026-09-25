# Delivery Planning — Clarifying Questions

## Sources

- [units] `inception/units-generation/unit-of-work.md`, `unit-of-work-dependency.md`, `unit-of-work-story-map.md` (9 units, their dependency map, and the stories in each)
- [contracts] `inception/contract-design/contract-summary.md`
- [scope] `ideation/scope-definition/scope-document.md`: value-first sequencing, with one hard ordering rule (the analysis output shape is defined before the hypothesis display) [scope Q4, Q5]
- [practices] `inception/practices-discovery/team-practices.md`: solo builder, no walking skeleton, squash-merge per Bolt
- [units Q4]: independent units may be built in parallel

Already settled and not re-asked:

- The overall approach is value-first, with the output-shape-before-display rule kept as a hard constraint.
- There is no thin end-to-end "walking skeleton" first.
- Independent work may run in parallel.

A **Bolt** is one build pass over a piece of the work (one or more units)
that ends in something that runs and can be demonstrated.

## Q1. Should the order come from a formal scoring model?

Value-first is already chosen. A formal model such as WSJF (Weighted
Shortest Job First) scores each Bolt as value, urgency, and risk reduction
divided by size, then ranks by score.

- A. No formal scoring: order Bolts value-first within the dependency map, and write the reasoning down for each Bolt
- B. Yes: score every Bolt with WSJF and follow the scores
- X. Other (please specify)

[Answer]: A (2026-09-25T15:06:40Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q2. How big should a Bolt be?

- A. Bundle closely related units: about 7 Bolts (foundation + contract together; guarded services + backend together; every other unit alone)
- B. One unit per Bolt: 9 Bolts
- X. Other (please specify)

[Answer]: A (2026-09-25T15:06:40Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q3. What outside dependencies could hold the build up, and what is the fallback?

Two things come from outside the project. The first is an LLM provider
account and key for the plain-language explanation, which is a Should Have.
The second is the public light-curve archive, which spike story US0.2 will
choose.

- A. Track both. If the LLM account is not ready, ship with the explanation marked "unavailable" (the product already handles that). If the archive choice slips, keep building against synthetic curves and committed fixtures, and add real curves when ready
- B. Treat the LLM account as blocking: do not start the backend Bolt without it
- X. Other (please specify)

[Answer]: A (2026-09-25T15:06:40Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q4. What worries you most about this build?

The biggest worry gets tackled as early as the dependency map allows.

- A. The analysis accuracy might be poor against real KOI labels: run a first evaluation soon after the analysis core exists, not at the very end
- B. The chart may be slow with 70,000-point curves in the browser: prove chart performance in the first frontend Bolt
- C. Running out of time: make sure every Bolt leaves something demoable
- D. The real-data archive might be hard to use: do the data-source spike first
- X. Other (please specify)

[Answer]: A (2026-09-25T15:06:40Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q5. Should each unit be built completely before the next one starts?

There are two ways to run the design and build steps across units. The
first designs and builds one unit fully, then moves to the next. That puts
working code in your hands after the first unit, and each Bolt ends with a
demo. The second does each design step for all units first, and writes code
last.

- A. One unit at a time: design and build each unit fully, in Bolt order
- B. Step by step across all units, with code last
- X. Other (please specify)

[Answer]: A (2026-09-25T15:06:40Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q6. How do you want to staff construction?

- A. I build every unit right here, one at a time, approving as we go
- B. Several teams each own a unit and approve their own work
- X. Other (please specify)

[Answer]: A (2026-09-25T15:06:40Z, **Mode:** chat — user accepted the recommended option: "all good")

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- No formal scoring model: Bolts are ordered value-first within the dependency map, with written reasoning per Bolt (Q1: A)
- 7 Bolts: B1 foundation + contract (U1+U2), B2 web explore (U6), B3 analysis core (U3), B4 guarded services + backend (U4+U5), B5 web investigate (U7), B6 curation (U8), B7 evaluation (U9) (Q2: A)
- External dependencies tracked with fallbacks: without the LLM account, ship with the explanation "unavailable"; if the archive choice slips, build on synthetic curves and fixtures (Q3: A)
- Biggest worry is analysis accuracy on real KOI labels: quick small-sample evaluation right after B3, full harness in B7, data-source spike pulled forward to B1 (Q4: A)
- Construction runs one unit at a time, designing and building each fully, in Bolt order (Q5: A)
- Staffing: one builder in this session, approving as we go (Q6: A)

Does this all look correct before I generate the delivery plan?

- Looks correct
- Request changes

[Answer]: Looks correct
