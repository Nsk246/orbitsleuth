**Collaborator:** aidlc-quality-agent

## Contribution

Angle: can each AC be written as a pass/fail test at a named level (U = unit, C = contract/schema, I = integration, E = e2e smoke, P = perf check that runs outside the blocking pre-merge gate), using fixtures only before merge (team.md Testing Posture)? Most ACs pass this check. The findings below are the ones that do not, grouped by severity.

### A. Hard-constraint backing (TC-4 / TC-5 / TC-6)

- **TC-5 → AC4.1.4 (C).** Well backed. Add explicit negative cases so the contract test has a definite list to cover. Rewrite: "Given the engine output contains a hypothesis whose `evidence` is missing, null, or `[]`, or whose `confidence` is missing, null, NaN, < 0 or > 100, When the output contract validates it, Then validation fails, the API returns the structured 'analysis failed' response (AC3.4.1), and no partial hypothesis list reaches the UI." Fold the 0–100 boundary out of AC4.1.2 into this AC. Values of exactly 0 and 100 must pass.
- **TC-4 → AC4.2.2 (U+C).** Weak. "When it is traced" is not an observable trigger. Rewrite: "Given a completed run, When its result is serialized, Then every evidence item carries a `sourceToolOutputId` that references a tool-output record in that run's trace, and the item's value equals that record's field. An evidence item with no matching record fails validation." Add **AC4.2.3 (I)**: "Given the explanation service is disabled or stubbed, When the same fixture curve is analyzed, Then the hypotheses, scores, ranking and evidence are identical to a run with the explanation enabled." This proves no ranking input comes from the LLM, and it makes AC4.3.3 automatable.
- **TC-6 → AC5.1.3 (I).** "Any code path" cannot be tested as written, because no test can enumerate every code path. Rewrite as bounded assertions on the single operation that owns finality: "Given a hypothesis with no recorded accept/reject action, When finalization is attempted directly, or through annotate-only, re-run (US5.3), or request-more-evidence (US5.4), Then the hypothesis stays non-final and the direct attempt returns an error." Also add a contract assertion: "engine/API output never carries a final status. Review status from the engine is always `unreviewed`." **Open point for design:** FR6.6 keeps review state in browser storage only. The design must name the component that owns the "final" transition (a frontend review store or an API endpoint), because the TC-6 integration test has to sit on that boundary.

### B. Vague or unmeasurable ACs: rewrites

- **AC1.2.3** "natural size / does not stretch": rewrite as "each card's rendered width equals the card width in a full row (±1 px)" (component test at 3 viewports).
- **AC2.1.2 / AC3.1.1 / AC3.2.2** (P): "mid-range laptop" and "p95" have no defined test rig or sample size. Specify the rig: "Chrome headless, 4x CPU throttle, p95 over 20 interactions/runs on the 70,000-point synthetic fixture." Define AC3.1.1's clock as running from the "Analyze" click to the hypothesis panel rendering. Mark all three as P, run on demand or nightly and never blocking pre-merge, so the fixture-only suite stays deterministic. Otherwise, mark them "Deferred" to NFR Requirements in traceability.
- **AC2.1.3**: name the keys, or reference a keymap set in design, and define the text summary: "target ID, point count, time range, and the currently visible range".
- **AC3.2.1**: "key inputs/outputs" needs a per-tool field list. Rewrite the Then clause: "...fields defined in the trace-step schema, each non-empty". This makes it a C test.
- **AC3.3.2** "the gap is stated": rewrite as "the result includes a notice naming each not-run check, and the confidence is strictly lower than for the paired fixture in which that check passes" (U, paired fixtures).
- **AC3.4.4** "never silently dropped" cannot be tested for "never". Rewrite as three fault-injection tests (I): "Given a fault injected at ingestion, at analysis, or at the API, Then an ERROR log record containing the run ID is emitted, the response is the structured failure, and the trace marks the failed step" (this also covers NFR6).
- **AC4.3.1** "every number it mentions matches": define the matching rule as "numbers extracted from the explanation equal a tool-output value after rounding to the displayed precision". Add the missing sad path as **AC4.3.4 (U)**: "Given a stubbed explanation containing a number that matches no tool output, When it is validated, Then the explanation is withheld and marked unavailable (same UI as AC4.3.2)."
- **AC4.4.1/AC4.4.2**: define the forbidden-term list as case-insensitive and fixed in one shared constant. Add the runtime sad path: "a generated explanation containing a forbidden term is withheld, not shown" (U). The check must also scan static UI strings.
- **AC4.5.1** "clear mismatch": point to named fixtures instead, e.g. "Given fixture `eb_odd_even_*` (odd/even depth ratio and secondary depth set in the fixture generator)" (U, fixtures-first).
- **AC5.4.1** "additional checks **or** an extended analysis" is non-deterministic. Pick one, or state which condition selects each. Add a sad path: "Given no further checks are available, Then the button is disabled with a reason."
- **AC6.1.2**: define "not silently skip" as "the run summary lists the failed target with its reason and the process exits non-zero". Test with a stubbed fetcher, with no live fetch.
- **AC6.5.2** "when I check it": rewrite as "the health endpoint returns 200 with a status field, and the error-rate counter increments by 1 per injected failure" (I).

### C. Missing boundary values

- **AC3.5.1 / AC6.4.1**: add "the 10th run within 60 min is live; the 11th is served from cache; a run 60 min after the first counted run is live again" (U with an injectable clock). "Per visitor" has no defined identity (IP? session?). Flag it for design; until then these ACs are only testable against a stubbed identity. Merge AC6.4.1 into AC3.5.1 so the same behavior is not asserted in two places. Refocus US6.4 on configuration validation (see D).
- **AC3.5.2 / AC6.4.2**: add "spend exactly equal to the cap blocks" and define "next day" (reset at 00:00 UTC).
- **AC3.3.1**: reference a named minimum-transit threshold, to be set in design, and test at threshold−1 (not run) and at threshold (runs).
- **AC5.2.1/5.2.2**: test at exactly 2,000 (saves) and 2,001 (rejected). Define how length is counted (Unicode code points, not UTF-16 units), because emoji make the two differ. Define empty and whitespace-only notes (not saved).
- **AC5.3.3**: test min, max, min−ε and max+ε for each parameter, and period-range min ≥ max (rejected). The ranges themselves are set in design.
- **AC4.1.1**: state "all four categories appear exactly once, sorted by descending confidence, with a deterministic tie-break" (C). Without a tie-break rule, AC3.1.3 can fail on ties.
- **AC3.1.3 / AC6.2.3**: name the fields excluded from the "identical" comparison (run ID, timestamps, durations). Otherwise the test is flaky by construction.
- **Curve size above 70,000 points**: add an AC to US2.1 covering what happens to a larger curve (downsampled with a notice, or rejected).

### D. Missing sad paths and coverage gaps

- **AC1.1.4 (new)**: "an empty catalog shows an empty-state message". **AC1.2.4 (new)**: "a filter with zero matches shows 'No curves match' and 'All' restores the full list". AC1.2.1 says "Real data" but FR1.3 says "Real". Align the label so a UI test has one target string.
- **AC3.2.4 (new)**: "Given the live-run stream disconnects mid-run, Then the trace marks the run interrupted and offers Retry" (I/component).
- **AC5.1.5 (new)**: can an accepted decision be changed to rejected? This is undefined, so the TC-6 test cannot pin state transitions. Decide and add an AC.
- **AC5.5.2**: key stored decisions by run ID so that AC5.3.2 (re-run keeps the earlier decision) is testable. Test "another visitor" as a fresh browser context (E or component).
- **US6.4**: add "Given a missing or invalid limit/cap value, When the service starts, Then it fails fast with a named config error", and "Given a paid-service secret env var is unset, Then LLM features are disabled and the explanation is marked unavailable (AC4.3.2)" (I).
- **NFR3**: only AC2.1.3 and AC5.1.4 touch it. Add a cross-cutting AC: "automated axe-core scan reports zero WCAG 2.1 AA violations on browser, workspace, and review views" (component/E).
- **NFR7**: add "Given an unknown or malformed target ID, When requested, Then the API returns a structured 4xx and no stack trace". Also, FR6.6 stores annotations only in the browser, so state which boundary validates annotation text.
- **FR4.8** (shared vocabulary) traces to no story. Add it to US4.1: "the frontend category enum equals the API schema enum" (C, generated types or snapshot).
- **AC6.2.4**: make it assertable. "Evaluation tests carry an `evaluation` pytest marker deselected by default; the CI workflow does not select it" (config test). Add **AC6.2.5 (U)**: "the harness logic runs pre-merge on a 3-row frozen fixture with a stubbed curve source", so FR8 code counts toward the 80% floor without live fetches.

### E. E2E smoke mapping (team.md: one or two)

- Smoke 1: US1.1 → US2.1 → US3.1 (pre-computed) → US4.1/US4.2 → US5.1 accept → reload keeps state (AC5.5.1).
- Smoke 2 (optional): the analysis-failed path (AC3.4.2).
- Every other AC stays at U/C/I.

## Positions

- AGREE: Q4 decision to keep sad paths inside the happy-path stories. Most of them are already present and map to U/I tests.
- AGREE: dedicated US3.4 (failed vs low-confidence). Its ACs AC3.4.1–AC3.4.3 are crisp contract/integration assertions.
- AGREE: AC4.1.4 as the TC-5 anchor, with the negative-case list added.
- OBJECT: AC5.1.3 wording ("any code path"). It cannot be tested as written; replace it with the bounded finalization assertions in A.
- OBJECT: AC4.2.2 wording ("When it is traced"). The trigger is not observable; the TC-4 test needs the `sourceToolOutputId` assertion plus the LLM-disabled equivalence test.
- OBJECT: gating AC2.1.2/AC3.1.1/AC3.2.2 inside the pre-merge suite without a defined rig. Classify them as perf checks outside the blocking gate, or Defer them to NFR Requirements.
- OBJECT: AC5.4.1 "checks or extended analysis". Behavior that is not deterministic cannot get a pass/fail test.
