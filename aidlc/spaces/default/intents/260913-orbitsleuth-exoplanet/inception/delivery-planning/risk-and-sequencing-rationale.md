# Risk and Sequencing Rationale — OrbitSleuth

A **Bolt** is one build pass over one or more units of work that ends in
something runnable. This file explains why the 7 Bolts in `bolt-plan.md`
come in that order.

## Heuristic

**Value-first** (Cohn-style value ordering), confirmed at scope definition
[scope Q4]. It keeps one hard ordering rule: the analysis output shape comes
before the hypothesis display [scope Q5] [project Corrections]. No formal
WSJF scoring is used [Q1]. WSJF, or Weighted Shortest Job First, ranks work
by value plus urgency plus risk reduction, divided by size. With one
decision-maker and 7 Bolts, written reasoning per Bolt gives the same
clarity.

"Value" here means demoable, visually compelling progress for the portfolio
audience, plus reduction of the biggest named risk, accuracy [Q4].

## Why This Order

| Position | Bolt | Reason |
|----------|------|--------|
| 1 | B1 Foundation and contract | Every unit depends on U1 and U2. The spike is pulled forward so real data can reach the accuracy check early [Q4]. |
| 2 | B2 Web explore | The fastest visible value: a polished browser and chart early. It also retires the frontend-performance risk (the 70,000-point chart). It needs only B1 and runs on fixtures. |
| 3 | B3 Analysis core | The heart of the rigor claim, built fixtures-first. Its small-sample real-data validation tackles the biggest worry (accuracy) before anything builds on the scoring [Q4]. |
| 4 | B4 Guarded services and backend | Connects the engine to the web. It can't come earlier (needs U3). The LLM fallback keeps it unblocked [Q3]. |
| 5 | B5 Web investigate | The headline demo: the full human-in-control loop. It must follow B3 (hard rule: output shape before display) and B4 (real API). |
| 6 | B6 Curation | Swaps fixture data for the real curated catalog. It needs the engine and RunService code (U3, U5). |
| 7 | B7 Evaluation | Publishes the full, reproducible baseline. The small B3 check has already de-risked it, so it can come last without betting the project on it. |

## Deviations From Pure Topological Order

- **Spike US0.2 pulled into B1.** US0.2 belongs to U8 (B6), but it is a
  one-day research note with no U8 code. Running it in B1 feeds the B3
  accuracy check and B6 early. This justified deviation is recorded here.
- **B2 before B3.** The dependency map allows either order, since both need
  only B1. B2 goes first for early visible value and to settle frontend
  performance.
- No Bolt runs before its dependencies.

## Risk Register

| Risk | Likelihood | Impact | Mitigation | Bolt |
|------|-----------|--------|------------|------|
| Rule-based scoring separates classes poorly on real KOI data | Medium | High | Small-sample validation in B3; tune before B4/B5; honest baseline in B7 | B3, B7 |
| Browser chart too slow at 70,000 points | Medium | Medium | Performance rig check in B2's DoD; display downsampling path (AC0.7.3) | B2 |
| Archive hard to access or slow | Medium | Medium | Spike in B1 with two alternatives; synthetic fixtures keep all Bolts unblocked | B1, B6 |
| LLM account or cost not ready | Low | Low | The explanation is a Should Have with an "unavailable" fallback; daily cap in B4 | B4 |
| Solo builder time runs short | Medium | Medium | Every Bolt ends in a demo; B5 is the headline demo and lands mid-plan | All |
| Contract drift between the Python and TypeScript sides | Low | Medium | U2 contract tests in CI from B1 | B1 onward |

## Sources

- [scope] `ideation/scope-definition/scope-document.md`, `scope-definition-questions.md`
- [project Corrections] `aidlc/spaces/default/memory/project.md`
- [units] `inception/units-generation/unit-of-work-dependency.md`
- `inception/delivery-planning/bolt-plan.md`
- [Q1], [Q3], [Q4] `inception/delivery-planning/delivery-planning-questions.md`

## Assumptions & Open Questions

None.
