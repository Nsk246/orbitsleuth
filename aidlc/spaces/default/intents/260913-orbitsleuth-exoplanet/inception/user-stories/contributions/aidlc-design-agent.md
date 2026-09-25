**Collaborator:** aidlc-design-agent

## Contribution

Angle: UX and persona fidelity. Checked against the confirmed user flow and
view states in `ideation/rough-mockups/user-flow.md` and `wireframes.md`,
NFR3 (WCAG 2.1 AA), NFR4 (breakpoints), and personas P1/P2. The journey
steps and error paths are well covered. The gaps are in screen states the
wireframes define but no AC tests, in accessibility (only US2.1 and US5.1
have AC), in responsive behavior (only US1.1 has AC), and in the
recruiter's 10-minute path.

### 1. Wireframe states with no acceptance criteria

The wireframes define five states per view. These have no AC:

- **US1.1 Loading**: add AC1.1.4: Given the catalog is loading, When the browser view renders, Then skeleton cards appear in the grid layout (not a blank page or a bare spinner), and a screen reader announces "Loading light curves". [mockups: View 1 Loading]
- **US1.2 Zero results**: the wireframe's Browser "Empty" state ("no data source configured") does not apply to a pre-curated static catalog (FR1.1). Replace it with AC1.2.4: Given a filter matches no entries, When the grid renders, Then I see "No light curves match this filter" and a "Show all" action, not an empty grid.
- **US3.1 Workspace Empty**: add AC3.1.4: Given a curve is loaded and not yet analyzed, When the workspace opens, Then the hypothesis panel shows "Run analysis to see results" with "Analyze" as its main action, and the trace panel is collapsed with the note "Run an analysis to see its trace". [mockups: View 2 and View 3 Empty]
- **US3.1/US3.2 Loading**: add AC3.1.5: Given an analysis is running, When the hypothesis panel updates, Then it names the current step (for example "Detrending...") rather than showing a bare spinner, and "Analyze" is disabled until the run ends. Disabling the button prevents duplicate runs that would use up the FR9.1 limit.
- **US3.4 Error, later steps**: extend AC3.4.2: the trace marks every step after the failed one as "not run". Later steps must not be left out. [mockups: View 3 Error]
- **US5.4 Failure path**: add AC5.4.3: Given the extra checks fail, When the result shows, Then the earlier hypotheses, evidence, and review decisions stay visible, and the failure message follows US3.4. Also add AC5.4.4: earlier accept/reject decisions stay unchanged, as in AC5.3.2.

### 2. Accessibility (NFR3, WCAG 2.1 AA): missing AC by story

- **US1.1**: add AC1.1.5: each card is one keyboard-focusable control with an accessible name that includes the target ID and type (for example "Open KOI-1234, real data"). The focus indicator must have at least 3:1 contrast against the dark theme (SC 2.4.7, 1.4.11).
- **US1.2**: add AC1.2.5: the filter is a group of toggle buttons that work with the keyboard and expose their state with `aria-pressed`. When the result count changes, it is announced through a polite live region (SC 4.1.3).
- **US2.1**: add AC2.1.5: focus management. When the workspace opens, focus moves to the `h1` target ID. "Back to Browser" (and the browser Back button) returns focus to the card I opened, with the previous filter kept. The hub-and-spoke IA depends on this to work for keyboard and screen-reader users.
- **US2.1 / US2.2**: add: the raw and detrended series and the folded event marker are told apart by more than color (line style or marker). Plotted lines have at least 3:1 contrast against the chart background (SC 1.4.1, 1.4.11). The text summary for screen readers changes when the view changes (raw, detrended, folded).
- **US2.3 AC2.3.2 (correction)**: a tooltip on a disabled control cannot be reached by keyboard or touch. Replace it with: "Folded" stays visible, is marked `aria-disabled`, and shows a short visible hint (for example "Available after a period is detected").
- **US3.2**: add AC3.2.4: trace steps update through `aria-live="polite"`. Each step status is text (OK / SKIPPED / FAILED / not run), never color alone. If `prefers-reduced-motion` is set, the replay in AC3.1.2 shows its steps without animation.
- **US4.1 / US4.2**: add: confidence is shown as a number with a text label, never as a color bar alone. "Supports" and "weakens" on evidence are shown with text or an icon plus a text label. Body text has at least 4.5:1 contrast on the dark theme (SC 1.4.3).
- **US5.1**: extend AC5.1.2: the status change is shown as a text badge and announced in a polite live region. Add AC5.1.5: Given I accepted or rejected a hypothesis, When I change my mind, Then I can change or clear the decision, and the latest recorded human action sets the status. This prevents errors and still satisfies TC-6.
- **US5.2**: add AC5.2.4: the note field has a visible label and a character counter. When 2,000 characters is exceeded, the validation message is linked to the field with `aria-describedby` and announced to screen readers (SC 3.3.1). The same pattern applies to the parameter validation in AC5.3.3.

### 3. Responsive behavior (NFR4): Workspace has no AC

P1 sometimes opens the link on a phone (personas). Add to US2.1:

- AC2.1.6: Given a screen narrower than 768 px, When the workspace renders, Then the chart is stacked above the hypothesis panel, the trace is a full-width section at the bottom, and the page does not scroll sideways at 320 px width (SC 1.4.10 Reflow). At 1024 px and wider, the chart and hypothesis panel are side by side. [mockups: View 2 Responsive]
- AC2.1.7: Given a touch screen, When I pinch or drag on the chart, Then it zooms or pans. Visible zoom-in, zoom-out, and reset buttons also work without gestures (SC 2.5.1).
- Open question for NFR design: NFR2 sets the 100 ms target only for a "mid-range laptop". The phone target is not defined. Recommend recording it as an assumption rather than leaving it out.

### 4. Recruiter's under-10-minute visit (P1): journey gap

The intent's "try in under 10 minutes" (requirements, Intent Analysis) is
not an AC on any story. Right now P1 arrives at 20–50 cards with no guidance
on where to start.

- Proposed **US1.4: Start from a featured example** (Should Have). As a recruiter, I want a clearly marked featured curve and a one-line description of what OrbitSleuth does, so that I reach an analyzed, evidence-backed result in my first minute. Origin: intent-statement success criterion (under 10 minutes), persona P1 goals. No new FR; it refines FR1.2. Delivery Planning decides whether it is in the MVP.
  - AC1.4.1: Given I land on the browser, When it loads, Then a one-sentence description and a "Start with a featured example" action appear above the grid without scrolling at every breakpoint.
  - AC1.4.2: Given I choose the featured example, When I click Analyze, Then a pre-computed result appears (NFR1, within 10 s), so the core flow takes at most 3 interactions from landing to a result that can be reviewed.
- **US6.3 AC6.3.2 (correction)**: this is a visitor-facing behavior placed under an author story. Move it to US4.1 or US1.4 as a visitor AC: a "How accurate is this?" link is visible in the workspace and leads to the latest metrics. This gives P1 the evidence of rigor. Its location is still set in design.

### 5. Non-expert comprehension (P2, and P1 with no astronomy background)

US4.3 (the LLM explanation) is Should Have and can be unavailable (AC4.3.2),
so it cannot be the only way a non-expert learns what the terms mean.
Proposed AC4.2.3 (Must Have): Given a technical term is shown (transit,
eclipsing binary, detrended, phase-folded, odd/even depth, secondary
eclipse, ranking score), When I activate its info control by mouse,
keyboard, or touch, Then I see a short, fixed plain-language definition
that does not depend on the explanation service. Origin: persona P2 pain
point "jargon without explanation" and the ideation readability rule. AC4.1.2's
"ranking score, not a calibrated probability" also needs a plain-language
version (for example "how this compares with the other explanations, not
the chance it is a planet").

## Positions

- AGREE: Three personas, with P3 given no public UI. This matches the solo-founder correction and keeps visitor stories clear.
- AGREE: Failure and edge cases as AC, plus the dedicated US3.4. The failed-vs-low-confidence distinction is the most important trust signal in the UI.
- AGREE: The advisory wording enforced by an automated check (US4.4). It turns a rule about copy into something testable.
- OBJECT: AC2.3.2's tooltip on a disabled control. It fails keyboard and touch access; use aria-disabled with a visible hint.
- OBJECT: Relying only on US4.3 (Should Have, can be unavailable) for non-expert comprehension. Add static term definitions as a Must Have AC (AC4.2.3).
- OBJECT: Keeping the wireframe's Browser "Empty = no data source configured" state. With a curated catalog, the real empty case is a filter with zero results (AC1.2.4).
- OBJECT: Leaving AC6.3.2 under the author persona. The recruiter-facing link to the rigor evidence should be a visitor AC that can be tested.
