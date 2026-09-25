# User Stories — OrbitSleuth

Personas: P1 Riya the Recruiter, P2 Casey the Curious Visitor, P3 Alex the
Author/Operator (see `personas.md`). "Visitor" means P1 or P2.

- **Priority** uses MoSCoW. The final MVP boundary is set in Delivery
  Planning.
- **Size**: every story is sized at about 1–3 days of work [Q3]. Engine,
  pipeline, and setup work is split into its own stories [Q5].
- **Test level** is tagged on each acceptance criterion (AC):
  - [U] unit
  - [C] contract/schema
  - [I] integration
  - [E] e2e smoke
  - [P] performance check, run on demand or nightly, never in the blocking
    pre-merge gate [practices].
- **Test data**: pre-merge tests use committed fixtures only [practices].
- **Performance rig for every [P] AC**: Chrome headless, 4x CPU throttle,
  p95 over 20 interactions or runs, on the 70,000-point synthetic fixture.

## Group 0 — Foundations (P3 enablers)

### US0.1 — Set up the repository and quality checks
**As the** author, **I want** a repository with automated lint, test, coverage, and security checks,
**so that** every later story lands on a safe, verifiable base.
**Priority**: Must Have. **Traces to**: NFR7, NFR10.

- **AC0.1.1** [I] Given a push to any branch, When CI runs, Then Black, Ruff (including `S` rules), Prettier, ESLint, pytest with coverage, and Vitest with coverage all run; the run fails if Python or TypeScript line coverage is below 80%.
- **AC0.1.2** [I] Given a dependency with a known vulnerability, When CI runs `pip-audit` / `npm audit`, Then the run fails and names the package.
- **AC0.1.3** [I] Given a commit containing a secret-shaped string, When the pre-commit gitleaks hook runs, Then the commit is blocked.
- **AC0.1.4** [C] Given the repository, When it is inspected, Then lockfiles for Python and JS are committed, and the backend and frontend live in separate top-level directories.

### US0.2 — Choose and verify the real light-curve source
**As the** author, **I want** a time-boxed (1-day) spike that picks and verifies the real-data archive and access method,
**so that** curation and evaluation build on a source that is real, current, and reachable.
**Priority**: Must Have. **Traces to**: FR1.5, FR8.1.

- **AC0.2.1** [U] Given the spike ends, When its note is read, Then it names the archive, the access method, and at least two alternatives considered, with a web-verified link for each.
- **AC0.2.2** [I] Given the chosen access method, When one known KOI target is fetched in a recorded script, Then a light curve with time and flux arrays is returned.

### US0.3 — Define the shared API contract
**As the** author, **I want** one shared schema for catalog, curve, result, evidence, trace, and error payloads,
**so that** the engine, API, and frontend use the same names and cannot drift.
**Priority**: Must Have. **Traces to**: FR4.8, FR3.5.
**Depends on**: US0.1.

- **AC0.3.1** [C] Given the schema, When the API serializes engine output, Then Python snake_case fields become camelCase only in the API layer.
- **AC0.3.2** [C] Given the frontend category type, When it is compared with the API schema enum, Then both list exactly `transit`, `eclipsing_binary`, `stellar_activity`, `noise`.
- **AC0.3.3** [C] Given engine output that no longer matches the API schema, When the contract test runs, Then it fails.
- **AC0.3.4** [C] Given any error, When the API responds, Then the body follows the one structured error shape.

### US0.4 — Build the analysis engine core: detrending and period search
**As the** author, **I want** a plain Python package that detrends a curve and runs a box-least-squares period search,
**so that** every hypothesis rests on real signal-processing tools.
**Priority**: Must Have. **Traces to**: FR3.1, FR3.2, FR3.3.
**Depends on**: US0.1.

- **AC0.4.1** [U] Given an injected-signal fixture with a known period, When the period search runs, Then the recovered period is within a tolerance set in functional design.
- **AC0.4.2** [U] Given the same input, parameters, and pipeline version, When the engine runs twice on the pinned lockfile and platform, Then outputs are identical at stored precision (6 decimal places).
- **AC0.4.3** [U] Given the engine package, When its imports are checked, Then it imports no web-framework module.
- **AC0.4.4** [U] Given a step fails (for example, no data after detrending), When the engine runs, Then it raises a typed domain error, never returns null.

### US0.5 — Add the vetting checks
**As the** author, **I want** depth, duration, signal-to-noise, odd/even depth, and secondary-eclipse checks,
**so that** hypotheses cite measurable evidence.
**Priority**: Must Have. **Traces to**: FR3.1, FR3.4.
**Depends on**: US0.4.

- **AC0.5.1** [U] Given a check runs, When it returns, Then its status is `pass`, `fail`, or `not_run`, with a reason.
- **AC0.5.2** [U] Given a curve with one transit below the minimum-transit threshold (set in functional design), When the odd/even check runs, Then it returns `not_run`; at exactly the threshold it runs.

### US0.6 — Score, rank, and validate hypotheses
**As the** author, **I want** a deterministic rule-based scorer that turns vetting results into four ranked hypotheses under a strict output contract,
**so that** confidence is reproducible and no bare verdict can escape.
**Priority**: Must Have. **Traces to**: FR4.1, FR4.2, FR4.4, FR3.4.
**Depends on**: US0.5, US0.3.

- **AC0.6.1** [C] Given any analysis, When the result is produced, Then all four categories appear exactly once, sorted by descending confidence, with a deterministic tie-break set in functional design.
- **AC0.6.2** [C] (TC-5) Given a hypothesis whose `evidence` is missing, null, or `[]`, or whose `confidence` is missing, null, NaN, below 0, or above 100, When the output contract validates it, Then validation fails and the API returns the structured "analysis failed" response. Values of exactly 0 and 100 pass.
- **AC0.6.3** [U+C] (TC-4) Given a completed run, When the result is serialized, Then every evidence item carries a `sourceToolOutputId` that references a tool-output record in that run's trace, and the item's value equals that record's field; an item with no matching record fails validation.
- **AC0.6.4** [U] Given a hypothesis whose score uses check C, When C is `not_run`, Then its confidence is strictly lower than when C returns the supporting result (all other inputs equal), and the result names the penalty.
- **AC0.6.5** [C] Given engine output, When it is inspected, Then the review status is always `unreviewed`; the engine and API never emit a final status.

### US0.7 — Serve the catalog and curves
**As the** author, **I want** API endpoints that return the catalog and curve data with validated IDs,
**so that** the browser and viewer have safe, compact data to show.
**Priority**: Must Have. **Traces to**: FR1.1, FR2.1, NFR7.
**Depends on**: US0.3.

- **AC0.7.1** [I] Given an unknown or malformed target ID, When it is requested, Then the API returns a structured 4xx with no stack trace.
- **AC0.7.2** [I] Given a 70,000-point curve, When it is requested, Then the payload is sent in a compact form (format set in design) rather than a verbose JSON object per point.
- **AC0.7.3** [I] Given a curve above 70,000 points, When it is requested, Then it is downsampled for display, with a notice in the response; analysis still uses the full data.

## Group 1 — Browse the catalog

### US1.1 — Browse curated light curves
**As a** visitor, **I want** to see the available light curves as cards,
**so that** I can pick one to investigate within seconds of arriving.
**Priority**: Must Have. **Traces to**: FR1.1, FR1.2, FR1.4, NFR3, NFR4.
**Depends on**: US0.7 (tested against a fixture catalog; not blocked by US6.1).

- **AC1.1.1** [I] Given the catalog loads, When I open the site, Then I see one card per curated entry (20–50 entries), each with its target ID and a curve thumbnail.
- **AC1.1.2** [I] Given the catalog request fails, When the browser view loads, Then I see an inline plain-language message with a Retry action and no raw error code.
- **AC1.1.3** [I] Given a viewport under 768 px, When I view the catalog, Then the cards show as one column; at 768–1023 px as two columns; at 1024 px and wider as four columns.
- **AC1.1.4** [I] Given the catalog is loading, When the view renders, Then skeleton cards appear in the grid layout and a screen reader announces "Loading light curves".
- **AC1.1.5** [I] Given an empty catalog, When the view renders, Then an empty-state message is shown, not a blank grid.
- **AC1.1.6** [I] Given keyboard use, When I tab through the cards, Then each card is one focusable control named with its target ID and type (for example "Open KOI-1234, real data"), with a focus indicator of at least 3:1 contrast.
- **AC1.1.7** [E] Given the browser, workspace, and review views, When an automated axe-core scan runs, Then it reports zero WCAG 2.1 AA violations.

### US1.2 — Filter real vs synthetic curves
**As a** curious visitor, **I want** to filter the catalog by Real / Synthetic / All,
**so that** I can focus on the kind of signal I care about.
**Priority**: Should Have. **Traces to**: FR1.3, NFR3.
**Depends on**: US1.1.

- **AC1.2.1** [I] Given the catalog is shown, When I select "Real", Then only entries from an archive are shown.
- **AC1.2.2** [I] Given I select "Synthetic", When the filter applies, Then only synthetic entries are shown.
- **AC1.2.3** [I] Given a filter matches fewer entries than one row, When the grid renders, Then each card's width equals its width in a full row (±1 px) at all three breakpoints.
- **AC1.2.4** [I] Given a filter matches nothing, When the grid renders, Then I see "No light curves match this filter" and a "Show all" action.
- **AC1.2.5** [I] Given keyboard use, When I operate the filter, Then it is a group of toggle buttons with `aria-pressed`, and the result count is announced through a polite live region.

### US1.3 — See where a light curve came from
**As a** visitor, **I want** to see each curve's provenance,
**so that** I know whether I am looking at real telescope data or an injected test signal.
**Priority**: Should Have. **Traces to**: FR1.5.
**Depends on**: US1.1.

- **AC1.3.1** [I] Given a real curve, When I open its details, Then I see its archive and mission.
- **AC1.3.2** [I] Given a synthetic curve, When I open its details, Then I see the injected period, depth, and noise level.

### US1.4 — Start from a featured example
**As a** recruiter, **I want** a one-line description of OrbitSleuth and a clear featured example,
**so that** I reach an evidence-backed result in my first minute.
**Priority**: Should Have. **Traces to**: FR1.2, NFR1 (origin: intent-statement "try in under 10 minutes" success criterion; persona P1) [Q6].
**Depends on**: US1.1, US3.1.

- **AC1.4.1** [I] Given I land on the browser, When it loads, Then a one-sentence description and a "Start with a featured example" action appear above the grid without scrolling, at every breakpoint.
- **AC1.4.2** [E] Given I choose the featured example, When I click Analyze, Then a pre-computed result appears, reaching a reviewable result in at most 3 interactions from landing.

## Group 2 — View the light curve

### US2.1 — Explore a light curve with zoom and pan
**As a** visitor, **I want** to see the selected curve plotted with zoom and pan,
**so that** I can look for dips and patterns myself.
**Priority**: Must Have. **Traces to**: FR2.1, FR2.4, NFR2, NFR3, NFR4.
**Depends on**: US1.1, US0.7.

- **AC2.1.1** [I] Given I select a card, When the workspace opens, Then the curve is plotted as flux against time and the `h1` is the target ID.
- **AC2.1.2** [P] Given the 70,000-point fixture on the performance rig, When I zoom or pan, Then each interaction responds within 100 ms (p95).
- **AC2.1.3** [I] Given keyboard use, When I focus the chart, Then I can zoom and pan with the keys in the design keymap. The chart has a text summary giving the target ID, point count, time range, and visible range.
- **AC2.1.4** [I] Given the curve fails to load, When the workspace opens, Then I see "This light curve could not be loaded" and a "Back to Browser" action.
- **AC2.1.5** [I] Given I open a curve, When the workspace loads, Then focus moves to the `h1`; when I go back, focus returns to the card I opened, and the previous filter is kept.
- **AC2.1.6** [I] Given a viewport under 768 px, When the workspace renders, Then the chart sits above the hypothesis panel, the trace is full-width at the bottom, and there is no horizontal scroll at 320 px. At 1024 px and wider, the chart and panel sit side by side.
- **AC2.1.7** [I] Given a touch screen, When I pinch or drag the chart, Then it zooms or pans, and visible zoom-in, zoom-out, and reset buttons work without gestures.

### US2.2 — Toggle raw and detrended views
**As a** visitor, **I want** to switch between the raw and detrended curve,
**so that** I can see what detrending removed before trusting the analysis.
**Priority**: Must Have. **Traces to**: FR2.2, NFR3.
**Depends on**: US2.1, US0.4 (the detrended series comes from the engine; the frontend computes nothing).

- **AC2.2.1** [I] Given a curve is plotted, When I choose "Detrended", Then the engine-provided detrended series replaces the raw series on the same time axis.
- **AC2.2.2** [I] Given I switch views, When the chart redraws, Then the current zoom range is kept.
- **AC2.2.3** [I] Given either view, When it renders, Then the series are distinguished by more than color, and plotted lines have at least 3:1 contrast. The screen-reader summary names the current view.

### US2.3 — View the curve folded at the detected period
**As a** visitor, **I want** to see the curve phase-folded at the detected period,
**so that** a repeating dip becomes visible as one clear shape.
**Priority**: Should Have. **Traces to**: FR2.3.
**Depends on**: US3.1 (period and phase come from the result payload).

- **AC2.3.1** [I] Given an analysis found a candidate period, When I choose "Folded", Then the curve is plotted against phase at that period, with the detected event marked by more than color.
- **AC2.3.2** [I] Given no candidate period was found, When I view the chart options, Then "Folded" stays visible, is marked `aria-disabled`, and shows the visible hint "Available after a period is detected".

## Group 3 — Run the analysis

### US3.1 — Get a fast analysis result for a curated curve
**As a** recruiter, **I want** a result within seconds of clicking "Analyze",
**so that** the demo feels responsive in a short visit.
**Priority**: Must Have. **Traces to**: FR3.2, FR7.1, FR7.2, NFR1.
**Depends on**: US2.1, US0.6, US6.3, US6.7.

- **AC3.1.1** [P] Given a curated curve with a pre-computed result, When I click "Analyze", Then the hypothesis panel renders within 10 s (p95, measured from the click).
- **AC3.1.2** [I] Given a pre-computed result, When it loads, Then the trace panel replays the recorded steps in order (detrend, period search, vetting).
- **AC3.1.3** [I] Given a curve is loaded and not yet analyzed, When the workspace opens, Then the hypothesis panel shows "Run analysis to see results" with "Analyze" as its main action, and the trace panel is collapsed with "Run an analysis to see its trace".
- **AC3.1.4** [I] Given an analysis is running, When the panel updates, Then it names the current step (for example "Detrending..."), and "Analyze" is disabled until the run ends.

### US3.2 — Watch a fresh analysis run step by step
**As a** curious visitor, **I want** to watch each analysis step appear as it runs,
**so that** I can see which tools were used and what they returned.
**Priority**: Must Have. **Traces to**: FR7.1, FR7.2, NFR1, NFR3.
**Depends on**: US3.1. (Step-update transport: polling, SSE, or WebSocket, chosen in design.)

- **AC3.2.1** [C] Given a fresh run, When each tool step completes, Then the trace shows its name, status, and duration, plus the fields defined in the trace-step schema, each non-empty.
- **AC3.2.2** [P] Given a fresh run on the 70,000-point fixture on the performance rig, When it runs, Then it completes within 30 s (p95).
- **AC3.2.3** [I] Given steps update, When each is added, Then it is announced through `aria-live="polite"`, and each status is text (OK / SKIPPED / FAILED / not run), never color alone. With `prefers-reduced-motion`, the replay shows steps without animation.
- **AC3.2.4** [I] Given the live-run connection drops mid-run, When it drops, Then the trace marks the run "interrupted" and offers Retry.

### US3.3 — See a partial result when a check cannot run
**As a** visitor, **I want** to see clearly when a vetting check could not run,
**so that** I do not assume the evidence is complete.
**Priority**: Must Have. **Traces to**: FR3.4.
**Depends on**: US3.1, US0.6.

- **AC3.3.1** [I] Given a fixture below the minimum-transit threshold, When the result is shown, Then the skipped check is listed as "not run" with its reason.
- **AC3.3.2** [I] Given a check did not run, When the result is shown, Then a notice names each not-run check and its confidence penalty (from AC0.6.4).

### US3.4 — Tell an analysis failure apart from a low-confidence result
**As a** visitor, **I want** a failed analysis to look clearly different from a weak but real result,
**so that** I never mistake a broken pipeline for "nothing found".
**Priority**: Must Have. **Traces to**: FR3.5, FR7.3, NFR6.
**Depends on**: US3.1, US6.7.

- **AC3.4.1** [C] Given a pipeline step raises an error, When the API responds, Then it returns the structured "analysis failed" response, not an empty or low-confidence hypothesis list.
- **AC3.4.2** [I] Given an analysis failed, When the workspace shows it, Then the hypothesis panel shows a plain-language failure message and Retry. The trace marks which step failed and why, and every later step as "not run".
- **AC3.4.3** [C] Given an analysis succeeded with all hypotheses at low confidence, When it is shown, Then it is presented as a valid result with evidence, never as an error.
- **AC3.4.4** [I] Given a fault injected at ingestion, at analysis, or at the API (three tests), When it occurs, Then an ERROR log record with the run ID is emitted, the response is the structured failure, and the trace marks the failed step.

### US3.5 — Understand when live runs are paused
**As a** visitor, **I want** a clear message when live runs are paused,
**so that** I still get a result and know why it is not a live run.
**Priority**: Must Have. **Traces to**: FR9.3.
**Depends on**: US3.1, US6.6 (enforcement lives in US6.6; this story is the visitor-facing behavior only).

- **AC3.5.1** [I] Given a limit or the cap blocks a live run and a cached result exists, When I request the run, Then I receive the cached result and a plain-language note that live runs are paused.
- **AC3.5.2** [I] Given no cached result exists (for example, a re-run with new parameters), When a limit blocks it, Then I see when live runs resume, and no error code.

## Group 4 — Review the hypothesis

### US4.1 — See ranked hypotheses with confidence
**As a** recruiter, **I want** to see the ranked hypotheses with a confidence score,
**so that** I can tell at a glance what the analysis finds most likely.
**Priority**: Must Have. **Traces to**: FR4.1, FR4.2, FR4.8, FR5.1, FR5.2, FR5.3, FR8.6, NFR3.
**Depends on**: US3.1.

- **AC4.1.1** [I] Given an analysis succeeded, When the result shows, Then the top hypothesis is shown first, followed by the other three in the order returned by the API.
- **AC4.1.2** [I] Given any hypothesis, When it shows, Then its confidence appears as a number with a text label, never as a color bar alone, and the label says in plain words that it is a ranking score: "how this compares with the other explanations, not the chance it is a planet".
- **AC4.1.3** [C] Given the API returns a result, When the frontend renders it, Then every shown score equals the API value; the frontend computes no scores.
- **AC4.1.4** [I] Given the workspace, When a result shows, Then a "How accurate is this?" link is visible and leads to the latest evaluation metrics (location set in design).
- **AC4.1.5** [I] Given the dark theme, When text renders, Then body text has at least 4.5:1 contrast.

### US4.2 — Inspect the evidence behind a hypothesis
**As a** curious visitor, **I want** to see each evidence item, which tool produced it, and what its terms mean,
**so that** I can judge the hypothesis myself.
**Priority**: Must Have. **Traces to**: FR4.2, FR4.3, FR4.4.
**Depends on**: US4.1.

- **AC4.2.1** [I] Given a hypothesis, When I open its evidence, Then each item shows the tool name, the measured value, and "supports" or "weakens" as text or an icon with a text label.
- **AC4.2.2** [I] (TC-4) Given the explanation service is disabled or stubbed, When the same fixture curve is analyzed, Then hypotheses, scores, ranking, and evidence are identical to a run with it enabled, excluding run ID, timestamps, and durations.
- **AC4.2.3** [I] Given a technical term is shown (transit, eclipsing binary, detrended, phase-folded, odd/even depth, secondary eclipse, ranking score), When I activate its info control by mouse, keyboard, or touch, Then I see a fixed plain-language definition that does not depend on the explanation service.

### US4.3 — Read a plain-language explanation
**As a** recruiter with no astronomy background, **I want** a short plain-language explanation of the result,
**so that** I understand it without knowing the jargon.
**Priority**: Should Have. **Traces to**: FR4.5, FR4.6, FR7.4.
**Depends on**: US4.2.

- **AC4.3.1** [U] Given a generated explanation, When it is validated at runtime, Then every number extracted from it equals a tool-output value after rounding to the displayed precision.
- **AC4.3.2** [I] Given the explanation service is unavailable, or its secret is unset, When the result shows, Then hypotheses and evidence still show, and the explanation area says it is unavailable.
- **AC4.3.3** [U] Given the ranked result is frozen before the explanation is generated, When its hash is compared before and after generation, Then it is unchanged.
- **AC4.3.4** [U] Given a stubbed explanation with a number that matches no tool output, When it is validated, Then it is withheld and shown as unavailable.
- **AC4.3.5** [I] Given an explanation is generated, When the trace updates, Then it shows the explanation step and which tool outputs it used.

### US4.4 — See advisory wording, never a "discovery" claim
**As a** visitor, **I want** results worded as advice, not confirmed facts,
**so that** I am not misled about what the analysis can prove.
**Priority**: Must Have. **Traces to**: FR4.7.
**Depends on**: US4.1.

- **AC4.4.1** [U] Given the forbidden-term list ("discovery", "discovered", "confirmed planet"), kept in one shared constant and matched case-insensitively, When static UI strings are scanned in CI, Then none contain a forbidden term.
- **AC4.4.2** [U] Given a generated explanation contains a forbidden term, When it is validated at runtime, Then it is withheld and shown as unavailable.

### US4.5 — See eclipsing binaries told apart from transits
**As a** curious visitor, **I want** eclipsing-binary signs shown as their own hypothesis,
**so that** I understand why a dip might not be a planet.
**Priority**: Must Have. **Traces to**: FR4.1, FR3.1.
**Depends on**: US4.2, US0.6.

- **AC4.5.1** [U] Given fixture `eb_odd_even_*` or `eb_secondary_*` (odd/even depth ratio and secondary depth set in the fixture generator and thresholds set in functional design), When analysis runs, Then `eclipsing_binary` ranks above `transit`, and the triggering check is cited.
- **AC4.5.2** [U] Given a synthetic single-planet transit fixture, When analysis runs, Then `transit` ranks first, and the odd/even and secondary-eclipse checks are cited as passing. (Behavior on real eclipsing binaries is measured by the evaluation, not asserted here.)

## Group 5 — Act on the hypothesis

### US5.1 — Accept, reject, or change a decision
**As a** visitor acting as reviewer, **I want** to accept or reject a hypothesis, and change my mind later,
**so that** nothing counts as final until a human has decided.
**Priority**: Must Have. **Traces to**: FR5.4, FR6.1, FR6.3, NFR3.
**Depends on**: US4.1.

- **AC5.1.1** [I] Given a new result, When it shows, Then every hypothesis has review status "unreviewed".
- **AC5.1.2** [I] Given I click Accept or Reject, When it saves, Then the status becomes "accepted" or "rejected", shown as a text badge and announced in a polite live region, and only then is the hypothesis final.
- **AC5.1.3** [I] (TC-6) Given a hypothesis with no recorded accept or reject action, When finalization is attempted directly, through annotate-only, through a re-run, or through request-more-evidence, Then it stays non-final, and the direct attempt returns an error. Only the Accept/Reject handlers of the review component can set a final status (the owning component is named in design).
- **AC5.1.4** [I] Given keyboard use, When I reach the action buttons, Then each is focusable and labeled.
- **AC5.1.5** [I] Given I accepted or rejected a hypothesis, When I change or clear the decision, Then the latest human action sets the status; clearing returns it to "unreviewed" (non-final) [Q7].

### US5.2 — Annotate a hypothesis
**As a** curious visitor, **I want** to add a note to a hypothesis,
**so that** I can record my reasoning whether or not I accept it.
**Priority**: Must Have. **Traces to**: FR6.2, NFR7.
**Depends on**: US5.1.

- **AC5.2.1** [U] Given a note of exactly 2,000 Unicode code points, When I save, Then it saves without changing the accept/reject status; at 2,001 it is rejected with a validation message.
- **AC5.2.2** [U] Given an empty or whitespace-only note, When I save, Then it is not saved.
- **AC5.2.3** [I] Given a note containing markup or script text, When it is shown again, Then it renders as plain text.
- **AC5.2.4** [I] Given the note field, When it renders, Then it has a visible label and a character counter, and a validation message is linked with `aria-describedby` and announced.

### US5.3 — Re-run analysis with adjusted parameters
**As a** curious visitor, **I want** to change the detrending window or period range and re-run,
**so that** I can test whether the hypothesis holds up.
**Priority**: Should Have. **Traces to**: FR6.4, FR6.7.
**Depends on**: US5.1, US3.2.

- **AC5.3.1** [I] Given a result, When I change the detrending window or period range and click Re-run, Then a new result is produced with its own run ID.
- **AC5.3.2** [I] Given an earlier result was accepted or rejected, When I re-run, Then the earlier decision is unchanged.
- **AC5.3.3** [U] Given each parameter's allowed range (set in design), When I enter min, max, min−ε, max+ε, or a period range with min ≥ max, Then min and max are accepted and the others show a labeled validation message, with no run started.

### US5.4 — Request more evidence
**As a** visitor, **I want** to request more evidence on a hypothesis,
**so that** I can decide with more confidence.
**Priority**: Should Have. **Traces to**: FR6.5.
**Depends on**: US5.1, US3.2.

- **AC5.4.1** [I] Given a result with a detected period P, When I click "Request more evidence", Then the workspace returns to loading and runs a harmonic check at P/2 and 2P plus a period search over a wider range (range set in design) [Q8].
- **AC5.4.2** [I] Given the extra checks finish, When the result shows, Then the new evidence items are marked as added.
- **AC5.4.3** [I] Given the extra checks fail, When the result shows, Then the earlier hypotheses, evidence, and review decisions stay visible, and the failure message follows US3.4.
- **AC5.4.4** [I] Given no detected period exists, When the result shows, Then "Request more evidence" is disabled with a visible reason.

### US5.5 — Keep my review decisions after a reload
**As a** curious visitor, **I want** my decisions and notes kept in my browser,
**so that** I can come back later and see my review.
**Priority**: Should Have. **Traces to**: FR6.6.
**Depends on**: US5.1, US5.2.

- **AC5.5.1** [E] Given I accepted a hypothesis and added a note, When I reload in the same browser, Then the status and note are still shown.
- **AC5.5.2** [I] Given stored decisions, When they are saved, Then they are keyed by run ID; a fresh browser context opening the same curve sees none of them.
- **AC5.5.3** [I] Given browser storage is unavailable, When I review, Then my decisions work for the session, and a note says they will not be kept.

## Group 6 — Operate the platform (P3)

### US6.1 — Define the catalog and generate synthetic curves
**As the** author, **I want** a catalog manifest with provenance fields and a seeded synthetic-curve generator,
**so that** the catalog has test signals with known answers.
**Priority**: Must Have. **Traces to**: FR1.1, FR1.5.
**Depends on**: US0.3.

- **AC6.1.1** [U] Given a seed and injection parameters, When the generator runs twice, Then it produces identical curves.
- **AC6.1.2** [C] Given the manifest, When it is validated, Then each entry has provenance: the archive and mission for a real curve, or the injected period, depth, and noise for a synthetic one.

### US6.2 — Fetch and store the real curves
**As the** author, **I want** to fetch the chosen real targets and store them with provenance,
**so that** the catalog has real telescope data.
**Priority**: Must Have. **Traces to**: FR1.1, FR1.5.
**Depends on**: US0.2, US6.1.

- **AC6.2.1** [I] Given a stubbed fetcher, When a target fails, Then the run summary lists it with its reason, and the process exits non-zero.
- **AC6.2.2** [U] Given stored curves, When they are saved, Then each has a checksum recorded in the manifest.

### US6.3 — Pre-compute catalog results
**As the** author, **I want** to pre-compute and store an analysis result for each catalog entry,
**so that** visitors get fast results.
**Priority**: Must Have. **Traces to**: FR3.3, NFR1.
**Depends on**: US0.6, US6.1.

- **AC6.3.1** [I] Given the catalog, When pre-computation runs, Then each entry has a stored result tagged with the pipeline version and run ID.
- **AC6.3.2** [I] Given a stored result's pipeline version differs from the current one, When it is served, Then it is flagged as stale.

### US6.4 — Freeze the KOI evaluation sample and label mapping
**As the** author, **I want** a versioned frozen sample of about 150 KOI targets and a versioned KOI-label-to-category mapping,
**so that** every evaluation uses identical, documented inputs.
**Priority**: Must Have. **Traces to**: FR8.1, FR8.2, FR8.4.
**Depends on**: US0.2.

- **AC6.4.1** [C] Given the sample file, When it is validated, Then it has about 150 targets with IDs and labels, and its input curves are cached and checksummed.
- **AC6.4.2** [U] Given a KOI label that does not map cleanly to one category, When results are scored, Then it is counted as "unmapped", not dropped.

### US6.5 — Run the evaluation and publish the baseline
**As the** author, **I want** to run the pipeline on the frozen sample and publish the metrics,
**so that** I can show measurable accuracy against real labels.
**Priority**: Must Have. **Traces to**: FR8.1, FR8.3, FR8.5, FR8.6, NFR5.
**Depends on**: US6.4, US0.6.

- **AC6.5.1** [I] Given the frozen sample, When the evaluation runs, Then every target is analyzed with the current pipeline version, and accuracy and per-category precision and recall are recorded with the pipeline version and date.
- **AC6.5.2** [I] Given the same sample, cached curves, pinned platform, and pipeline version, When the evaluation runs twice, Then the metrics are identical.
- **AC6.5.3** [C] Given the test configuration, When the default pre-merge suite runs, Then tests marked `evaluation` are deselected.
- **AC6.5.4** [U] Given a 3-row frozen fixture and a stubbed curve source, When the harness logic runs pre-merge, Then it computes the expected metrics.

### US6.6 — Configure and enforce cost and rate limits
**As the** author, **I want** a per-visitor rate limit, a daily spending cap, and a cost meter set in configuration,
**so that** running costs stay within budget.
**Priority**: Must Have. **Traces to**: FR9.1, FR9.2, NFR8, NFR7.
**Depends on**: US0.3.

- **AC6.6.1** [U] Given a limit of 10 live runs per hour per visitor (best-effort identity: hashed client IP or anonymous cookie, set in design), and an injectable clock, When runs are counted, Then the 10th run within 60 min is live, the 11th is blocked, and a run 60 min after the first counted run is live again. Only live runs count; serving a pre-computed result never does.
- **AC6.6.2** [U] Given each paid LLM call, When it completes, Then the cost meter adds estimated tokens × configured unit price. Spend exactly equal to the cap blocks further live runs for all visitors until reset at 00:00 UTC.
- **AC6.6.3** [I] Given a missing or invalid limit or cap value, When the service starts, Then it fails fast with a named configuration error.
- **AC6.6.4** [I] Given secrets for paid services, When the service starts, Then it reads them only from environment variables.

### US6.7 — Trace and reproduce any run
**As the** author, **I want** each run to carry a run ID and pipeline version linked to its logs,
**so that** I can reproduce and debug any result.
**Priority**: Must Have. **Traces to**: FR3.3, NFR9.
**Depends on**: US0.3 (built before US3.4, which logs errors by run ID).

- **AC6.7.1** [I] Given any analysis run, When I look up its run ID, Then I find its pipeline version, input ID, trace, and log entries.
- **AC6.7.2** [I] Given the service is running, When the health endpoint is called, Then it returns 200 with a status field, and the error-rate counter increases by 1 per injected failure.

## Dependency Summary

```
US0.1 -> US0.3, US0.4
US0.4 -> US0.5 -> US0.6
US0.3 -> US0.6, US0.7, US6.1, US6.6, US6.7
US0.2 -> US6.2, US6.4
US0.7 -> US1.1, US2.1
US6.1 -> US6.2, US6.3
US0.6 -> US6.3, US6.5, US3.3, US4.5
US1.1 -> US1.2, US1.3, US2.1
US2.1 -> US2.2, US3.1
US3.1 -> US1.4, US2.3, US3.2, US3.3, US3.4, US3.5, US4.1
US4.1 -> US4.2, US4.4, US5.1
US4.2 -> US4.3, US4.5
US5.1 -> US5.2, US5.3, US5.4, US5.5
US6.4 -> US6.5
US6.6 -> US3.5
US6.7 -> US3.1, US3.4
```

Critical path for the core demo:
US0.1 -> US0.4 -> US0.5 -> US0.6 -> US6.3 -> US3.1 -> US4.1 -> US4.2 -> US5.1
(US0.3 -> US0.7 -> US1.1 -> US2.1 runs in parallel and joins at US3.1.)

## E2E Smoke Tests

- **Smoke 1**: US1.1 -> US2.1 -> US3.1 (pre-computed) -> US4.1/US4.2 -> US5.1 accept -> reload keeps the decision (AC5.5.1).
- **Smoke 2** (optional): the analysis-failed path (AC3.4.2).

## Mob Review Outcome

- **Designer**: added the screen states, accessibility, and responsive ACs; the featured-example story (US1.4); the visitor accuracy link (AC4.1.4); and fixed term definitions (AC4.2.3). All objections resolved.
- **Developer**: split the engine, pipeline, and evaluation work (US0.4–US0.6, US6.1–US6.5); added setup stories (US0.1–US0.3, US0.7); rewrote the confidence-penalty and eclipsing-binary ACs; set the rate-limit identity and cost meter. All objections resolved.
- **Quality engineer**: added test levels, boundary values, a performance rig, fault-injection tests, and the bounded TC-6 assertions; merged the duplicate limit ACs. All objections resolved.
- **Judgment calls** decided by the user: Q5–Q8 in the questions file.
- **Maintained dissent**: none.

## Sources

- [requirements] `inception/requirements-analysis/requirements.md`
- [personas] `inception/user-stories/personas.md`
- [mockups] `ideation/rough-mockups/user-flow.md`, `wireframes.md`
- [practices] `inception/practices-discovery/team-practices.md`
- [Q1]–[Q8] `inception/user-stories/user-stories-questions.md`
- Contributions: `inception/user-stories/contributions/aidlc-design-agent.md`, `aidlc-developer-agent.md`, `aidlc-quality-agent.md`

## Assumptions & Open Questions

- Set in design: the location of the published evaluation results (AC4.1.4).
- Set in design: the trace-step schema (AC3.2.1).
- Set in design: the step-update transport (US3.2).
- Set in design: the rate-limit visitor identity (AC6.6.1).
- Set in design: the parameter ranges (AC5.3.3).
- Set in design: the minimum-transit threshold (AC0.5.2).
- Set in design: the eclipsing-binary thresholds (AC4.5.1).
- Set in design: the ranking tie-break (AC0.6.1).
- Set in design: the component that owns the final review status (AC5.1.3).
- The 70,000-point curve ceiling is still an assumption from requirements.
- The phone performance target is not defined; it is flagged for NFR design.
- The story count is 36, slightly above the "about 34" agreed in Q5, because the engine and evaluation work split into more pieces.
