**Collaborator:** aidlc-quality-agent

## Contribution

Overall the draft's Testing Posture section is a sound starting point — it
correctly keeps the `mvp` scope's `test-after` / 80%-line-coverage default,
correctly uses the two structured fields (`Methodology`, `Ordering`) the
integration template requires, and correctly ties an accuracy-fixture note
to the confirmed success metric. My review focuses on what is still missing
for a workable quality gate: concrete tooling, test-data strategy for the
hard constraints already confirmed in `discovered-rules.md`, and how the
test pyramid maps onto this specific architecture (Python analysis engine +
tool-backed calls + interactive frontend + human review gate).

**1. Coverage tooling is unnamed — needs to be resolved before "80% floor"
is enforceable.**
The draft states an 80% line-coverage floor but names no measurement tool
for either language. Recommend the interview confirm:
- Python: `pytest` + `pytest-cov` (coverage.py backend), floor enforced via
  `--cov-fail-under=80` in the test command.
- JS/TS: `Vitest` (or `Jest`) with its built-in `c8`/`istanbul` coverage
  reporter, floor enforced via the runner's coverage-threshold config.
Without naming these now, "CI execution before merge" in the draft's own
Testing Posture bullet has nothing concrete to execute.

**2. "CI execution before merge" needs disambiguation against the scope
document's deployment exclusion.**
`scope-document.md` excludes "Production-grade AWS deployment (staging/prod
environments, **CI/CD**, autoscaling)" from this MVP. The draft's Testing
Posture bullet reads that exclusion correctly (deployment CI/CD is out,
solo pre-merge test execution is in) but doesn't say *where* that execution
happens. Since `project.md`'s `## Corrections` frames OrbitSleuth as a
resume/portfolio artifact for a hiring-manager audience, I'd flag a
non-binding recommendation for the interview: a lightweight GitHub Actions
workflow that runs lint + tests + coverage on every push (no deployment
step) is free, satisfies "CI execution before merge" literally, and gives
the portfolio a visible, verifiable quality signal (Actions badge/history)
that a purely local pre-merge script cannot. This does not conflict with
the BC-2 "no provisioning before Unit-of-Work approval" constraint — a
test-only Actions workflow provisions no cloud resources. Worth presenting
as an option in the interview, not asserting as decided.

**3. The three confirmed hard constraints in `discovered-rules.md` need
explicit test obligations, not just documentation.**
TC-4 (real tool-invoked analysis, never free-form LLM reasoning), TC-5
(every hypothesis must cite evidence + confidence, never a bare verdict),
and TC-6 (explicit human accept/reject/annotate before a hypothesis is
final) are all testable product invariants, and none currently has a
corresponding test-pattern note in `team-practices.md`. Recommend adding to
`## Testing Posture`:
- A contract/schema test on the analysis engine's output (evidence list +
  confidence score always present, never null/bare) — this also directly
  serves the confirmed cross-layer dependency (`intent-backlog.md` #3→#5:
  output shape must be defined before frontend hypothesis-display design),
  since a schema test is the executable form of that shape contract.
  Recommend the test type be `contract` rather than a generic frontend/API
  unit test — this bridges the pyramid layer between Python analysis and
  the frontend the draft doesn't currently name explicitly.
- A negative test asserting the system cannot mark a hypothesis "final"
  without a human action recorded (TC-6) — this is a good candidate for a
  small integration test rather than a unit test, since it spans the
  review-workflow boundary.
- A provenance assertion in the analysis-layer tests confirming hypothesis
  evidence traces back to actual tool output objects (period-detection,
  detrending, vetting-statistics results), not synthesized text — this is
  the executable form of TC-4 and should sit alongside the accuracy-fixture
  tests the draft already proposes.

**4. Test-data strategy needs a fixture/network split.**
`intent-backlog.md` capability 1 calls for both real archival light curves
and synthetic/injected-signal ones. For deterministic, repeatable CI runs
(a Key Principle for this role), the analysis-layer unit and accuracy tests
should run against committed synthetic/injected-signal fixtures and a small
frozen sample of labeled KOI-table rows, not live NASA archive fetches —
live-archive access should be reserved for the confirmed evaluation
methodology (backlog #8) as a separate, explicitly-labeled validation run
(manual or scheduled), not part of the pre-merge suite. The draft's
accuracy-fixture note is good but should say this explicitly, or the
pre-merge suite risks flaking on network/archive availability.

**5. Consider a mixed methodology rather than uniform test-after.**
The stage's own integration guidance allows `custom` when cadence differs
by layer. Given the analysis engine has an objective, pre-existing
correctness target (accuracy against the labeled KOI table) while the
frontend's correctness is more behavioral/visual, the interview could
usefully ask whether the human wants test-first (or at least
fixtures-first: write the KOI-labeled fixture assertions before the
detection logic) specifically for the analysis/vetting layer, with
test-after retained for the API boundary and frontend. This is an
alternative to flag, not a correction — `test-after` uniformly is a
legitimate answer too, but the draft doesn't currently surface the option.

**6. Pyramid shape and e2e scope should be named.**
With no accounts and no multi-user flows out of scope, the e2e surface is
naturally small — a single happy-path e2e/smoke test of the confirmed value
stream (select light curve → run analysis → see ranked hypothesis with
evidence → accept/reject/annotate) is enough; the draft doesn't currently
say how many e2e tests are expected, which risks either zero (undertested
integration) or an ice-cream-cone (over-invested UI e2e for a solo
project). Recommend `team-practices.md` state this explicitly: unit-heavy
(analysis functions, component logic), a handful of integration/contract
tests at the analysis↔frontend boundary and the human-review-gate
invariant, and exactly one or two e2e smoke tests covering the full value
stream.

**7. Frontend test tooling is unnamed.**
Code Style names Prettier/ESLint for JS/TS but Testing Posture never names
a frontend test runner or component-testing library (e.g. Vitest + React
Testing Library, or the frontend framework's idiomatic equivalent, plus
Playwright if an e2e smoke test is affirmed per point 6). This should be
resolved at the interview alongside the frontend framework choice if not
already fixed elsewhere.

## Positions

- AGREE: keeping `test-after` as the scope-default `Methodology` with the
  80%-line-coverage floor from `mvp` — a reasonable default absent a
  stronger team preference — one-line rationale: matches org.md's
  documented scope table and nothing in the confirmed constraints demands
  stricter TDD.
- AGREE: the added accuracy-fixture note tying analysis-layer testing to
  the KOI-table success metric — directly traceable to a confirmed success
  metric and correctly kept as an addition rather than a replacement of the
  structured fields.
- OBJECT: the draft names no concrete coverage-measurement tooling for
  either language — an 80% floor is unenforceable without naming
  `pytest-cov`/equivalent and a JS/TS coverage reporter, so this should be
  added before or during the interview rather than left implicit.
- OBJECT: the draft's three confirmed hard constraints (TC-4/TC-5/TC-6) have
  no corresponding test-pattern obligations in `## Testing Posture` — a
  hard constraint that is only documented and never covered by a specific
  test type is a gap the interview or integration pass should close.
- OBJECT: the accuracy-fixture testing note doesn't specify a fixture/
  network split — without it, pre-merge tests risk depending on live NASA
  archive access, which is neither deterministic nor "fast," undermining
  the "CI execution before merge" bullet it sits next to.
