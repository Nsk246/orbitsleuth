# Interaction Specification — OrbitSleuth

Component specs follow `component-spec-template.md`. Breakpoints:

- mobile: < 768 px
- tablet: 768–1023 px
- desktop: ≥ 1024 px

Every component is built on a headless accessible primitive where one exists
[Q1]. All motion stays under 300 ms and is disabled under
`prefers-reduced-motion`.

## Global Interaction Rules

- **Navigation (hub-and-spoke)**: Browser → Workspace → Back.
  - Opening a curve moves focus to the Workspace `h1`.
  - "Back to Browser" and the browser Back button restore the previous
    filter and focus the card that was opened (AC2.1.5).
- **Feedback**
  - Every async action shows its status in place.
  - Status changes are announced through one polite live region per view.
  - No raw error codes are ever shown.
- **Error prevention**
  - "Analyze" is disabled while a run is in progress (AC3.1.4).
  - Invalid parameters block a re-run with inline messages (AC5.3.3).
  - Decisions are reversible (AC5.1.5), so no confirmation dialogs are
    needed.
- **Touch targets**: at least 44×44 px, with 8 px spacing.

---

## CurveCard

| Field | Value |
|---|---|
| Component | CurveCard |
| Description | Catalog entry that opens a curve's workspace |
| Category | navigation |

### States

| State | Description | Trigger |
|---|---|---|
| default | Thumbnail, target ID, type badge, Source button | catalog loaded |
| hover | Border brightens to `color.border.focus` | mouseover |
| focus | 2 px focus ring, ≥ 3:1 contrast | Tab |
| loading | Skeleton shape | catalog loading |
| featured | "★ Featured" text badge | manifest flag |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| targetId | string | yes | — | Display ID and accessible name |
| kind | "real" \| "synthetic" | yes | — | Badge icon and text |
| thumbnail | number[] | yes | — | Sparkline series from the API |
| provenance | object | yes | — | Popover content (US1.3) |
| featured | boolean | no | false | Featured badge |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| mobile | Full-width list row |
| tablet | Half-width grid cell |
| desktop | Quarter-width grid cell |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | Native `<a>` for the card; native `<button>` for Source |
| Keyboard interaction | Tab to the card, Enter to open; Tab to Source, Enter/Space opens the popover |
| Label / aria-label | "Open KOI-1234, real data" |
| Contrast ratio | Text ≥ 4.5:1; badge and border ≥ 3:1 |
| Screen reader | The name includes the type; the thumbnail is `aria-hidden` |
| Focus management | Remembered as the return target when coming back from the Workspace |

---

## FilterToggleGroup

| Field | Value |
|---|---|
| Component | FilterToggleGroup |
| Description | Real / Synthetic / All catalog filter |
| Category | input |

### States

| State | Description | Trigger |
|---|---|---|
| default | "All" pressed | page load |
| focus | Focus ring on the active item | Tab |
| pressed | `aria-pressed="true"`, filled style | click / Enter / Space |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| value | "real" \| "synthetic" \| "all" | yes | "all" | Current filter |
| count | number | yes | — | Shown and announced as the result count |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| mobile | Wraps below the count; buttons are full-height touch targets |
| tablet / desktop | Inline, left of the count |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | `role="group"` with `aria-label="Filter light curves"`; toggle buttons use `aria-pressed` |
| Keyboard interaction | Tab through the buttons; Enter/Space toggles |
| Label / aria-label | The visible text labels |
| Contrast ratio | ≥ 3:1 between the pressed and unpressed states, plus a pressed-state checkmark icon |
| Screen reader | The polite live region announces "12 light curves shown" |
| Focus management | Focus stays on the pressed button |

---

## LightCurveChart

| Field | Value |
|---|---|
| Component | LightCurveChart |
| Description | Zoomable flux-vs-time plot with raw, detrended, and folded views |
| Category | display |

### States

| State | Description | Trigger |
|---|---|---|
| default | Raw series, full range | curve loaded |
| focus | Focus ring on the chart region; keyboard pan and zoom active | Tab |
| loading | Skeleton plot area | curve loading |
| error | "This light curve could not be loaded." + Back to Browser | fetch failed |
| disabled-view | "Folded" is `aria-disabled` with a visible hint | no period yet |
| downsampled | Notice: "Showing a simplified view of 140,000 points" | curve over 70,000 points (AC0.7.3) |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| series | {time[], flux[]} | yes | — | Raw series from the API |
| detrended | {time[], flux[]} | no | — | From the engine; the Detrended view is enabled when present |
| fold | {period, phase[], flux[], eventPhase} | no | — | From the result; enables Folded |
| view | "raw" \| "detrended" \| "folded" | yes | "raw" | Current view; the zoom range is kept across raw and detrended |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| mobile | Full width, 240 px tall; pinch zoom and drag pan; visible + / − / Reset buttons |
| tablet | Full width, 320 px tall |
| desktop | About 60% width, 420 px tall |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | `role="img"` on the plot, with a linked text summary (`aria-describedby`) |
| Keyboard interaction | Arrow ←/→ pan; + / − zoom; 0 reset; view switch is a toggle group (same pattern as the filter) |
| Label / aria-label | "Light curve for KOI-1234, raw view" |
| Contrast ratio | Plot line ≥ 3:1 against the chart background; raw is a solid line, detrended a dotted line, the folded event marker a triangle (not color alone) |
| Screen reader | The summary gives target ID, view, point count, time range, and visible range, and updates on view or zoom change |
| Focus management | Focus stays on the chart during keyboard zoom/pan |

---

## HypothesisPanel (TopHypothesisCard + RankedList)

| Field | Value |
|---|---|
| Component | HypothesisPanel |
| Description | Top hypothesis card, ranked list of the other three, review actions |
| Category | display / feedback |

### States

| State | Description | Trigger |
|---|---|---|
| empty | "Run analysis to see results" + [Analyze] | curve loaded, no run |
| loading | Current step name + "n of N"; Analyze disabled | run in progress |
| success | Top card + ranked list + evidence + explanation | result received |
| partial | Success + a "checks not run" notice naming the penalty | any `not_run` check |
| error | Failure card distinct from low-confidence results + [Retry] | structured failure response |
| paused | Cached result + info banner, or resume-time message | limit or cap reached |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| result | AnalysisResult | no | — | API result, rendered as-is; no client-side scoring |
| runState | "idle" \| "running" \| "done" \| "failed" \| "paused" | yes | "idle" | Drives the state |
| reviewByHypothesis | map | yes | {} | Review status per run ID and hypothesis |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| mobile | Stacked below the chart, one column |
| tablet | Two columns (the top card and ranked list; evidence) |
| desktop | About 40% right column |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | `region` labeled "Hypothesis"; the ranked list is `<ol>`; each row is a disclosure button (`aria-expanded`) |
| Keyboard interaction | Tab through the actions; Enter/Space expands a row |
| Label / aria-label | Score text: "Eclipsing binary, ranking score 41" |
| Contrast ratio | Text ≥ 4.5:1; score bars ≥ 3:1, with the number always shown |
| Screen reader | Status changes ("Accepted") are announced politely |
| Focus management | After Accept/Reject, focus moves to the new [Change decision] control; after Retry, focus returns to the panel heading |

---

## ReviewActions (Accept / Reject / Change decision / Add note)

| Field | Value |
|---|---|
| Component | ReviewActions |
| Description | The only place a hypothesis can become final (TC-6) |
| Category | input |

### States

| State | Description | Trigger |
|---|---|---|
| default | Status UNREVIEWED; [Accept] [Reject] [Add note] | new result |
| decided | Badge ACCEPTED or REJECTED; [Change decision ▾] offering Accept, Reject, Clear | click Accept/Reject |
| focus | Focus ring | Tab |
| storage-unavailable | Inline note: "Your decisions won't be kept after you leave." | storage error (AC5.5.3) |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| runId | string | yes | — | Storage key (AC5.5.2) |
| hypothesis | category enum | yes | — | Target of the decision |
| status | "unreviewed" \| "accepted" \| "rejected" | yes | "unreviewed" | Current status |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| mobile | Buttons full width, stacked |
| tablet / desktop | Inline row |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | Native buttons; Change decision is a menu-button primitive |
| Keyboard interaction | Enter/Space activates; the menu uses arrow keys and closes with Escape |
| Label / aria-label | "Accept transit hypothesis", "Reject transit hypothesis" |
| Contrast ratio | ≥ 4.5:1 text; the badge is text, not color |
| Screen reader | Polite announcement: "Transit hypothesis accepted" |
| Focus management | Focus moves to Change decision after a decision; returns to the Accept button after Clear |

---

## NoteEditor

| Field | Value |
|---|---|
| Component | NoteEditor |
| Description | Inline plain-text note per hypothesis |
| Category | input |

### States

| State | Description | Trigger |
|---|---|---|
| collapsed | [Add note] button | default |
| editing | Labeled textarea, counter "0 / 2,000", [Save] [Cancel] | click Add note |
| error | "Notes can be up to 2,000 characters." linked via `aria-describedby` | over 2,000 code points |
| saved | Note shown as plain text + [Edit] | Save with valid text |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| value | string | no | "" | Note text; empty or whitespace-only is not saved |
| maxLength | number | no | 2000 | Counted in Unicode code points |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| all | Full width of the panel; the textarea grows up to 8 lines |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | Native `<textarea>` with a visible `<label>` |
| Keyboard interaction | Tab into the field; Save/Cancel are buttons; Escape cancels |
| Label / aria-label | "Note on transit hypothesis" |
| Contrast ratio | ≥ 4.5:1 text; border ≥ 3:1 |
| Screen reader | The counter is announced at 90% and when over the limit |
| Focus management | Focus moves to the textarea on open; returns to Add note/Edit on close |

---

## EvidenceGroups and TermHelp popover

| Field | Value |
|---|---|
| Component | EvidenceGroups + TermHelp |
| Description | Supports / Weakens / Not run groups; "?" plain-language definitions |
| Category | display / feedback |

### States

| State | Description | Trigger |
|---|---|---|
| default | Groups with items (icon + value + tool name) | result received |
| added | New items tagged "Added" after Request more evidence | extra checks done (AC5.4.2) |
| popover-open | Definition text next to the term | click / Enter / Space on "?" |
| focus | Focus ring on "?" | Tab |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| evidence | EvidenceItem[] | yes | — | Each has `sourceToolOutputId`, value, effect, tool |
| termKey | enum of 7 glossary terms | yes | — | Fixed definition text; no LLM involved (AC4.2.3) |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| mobile | Groups stacked; the popover is anchored and full width |
| tablet / desktop | Groups stacked in the panel; the popover is anchored beside the term |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | Groups are `<section>` with `<h3>` and `<ul>`; TermHelp is a button plus a non-modal popover (`aria-expanded`, `aria-controls`) |
| Keyboard interaction | Enter/Space toggles; Escape closes and returns focus to "?" |
| Label / aria-label | "What is odd/even depth?" |
| Contrast ratio | ≥ 4.5:1; the ✓ ✗ – icons always appear with their group heading text |
| Screen reader | The popover content is read on open |
| Focus management | Focus stays on the "?" button; the popover does not trap focus |

---

## TracePanel

| Field | Value |
|---|---|
| Component | TracePanel |
| Description | Expandable ordered list of pipeline steps |
| Category | feedback |

### States

| State | Description | Trigger |
|---|---|---|
| disabled | Collapsed, "Run an analysis to see its trace" | no run |
| streaming | Steps appended live | fresh run |
| replay | Stored steps revealed in sequence (instantly under reduced motion) | pre-computed result |
| complete | All steps with status | run done |
| failed | Failed step FAILED + reason; later steps "not run" | failure |
| interrupted | "Connection lost — the run was interrupted." + [Retry] | stream dropped (AC3.2.4) |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| steps | TraceStep[] | yes | [] | Schema set in design (AC3.2.1) |
| runId | string | yes | — | Shown in the header |
| pipelineVersion | string | yes | — | Shown in the header |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| mobile | Full width at the page bottom; the key output wraps to a second line |
| tablet / desktop | Full width below the chart and panel |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | Disclosure button + `region`; the steps are `<ol>`; updates via `aria-live="polite"` |
| Keyboard interaction | Enter/Space expands or collapses |
| Label / aria-label | "Analysis trace, 5 steps" |
| Contrast ratio | Status text ≥ 4.5:1; the status is text, never color alone |
| Screen reader | Each new step is announced: "Step 3, period search, OK" |
| Focus management | Focus stays on the disclosure button |

---

## RerunDisclosure ("Adjust and re-run")

| Field | Value |
|---|---|
| Component | RerunDisclosure |
| Description | Collapsed parameter form for re-running an analysis |
| Category | input |

### States

| State | Description | Trigger |
|---|---|---|
| collapsed | "▸ Adjust and re-run" | default |
| expanded | Labeled number fields with the allowed range shown, + [Re-run analysis] | click |
| error | Inline message per field; Re-run disabled | out of range, or period min ≥ max |
| disabled | Hint "Live analysis is paused" | limit or cap reached |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| detrendWindowHours | number | yes | engine default | Range set in functional design |
| periodMinDays / periodMaxDays | number | yes | engine default | Range set in functional design |

### Responsive Behaviour

| Breakpoint | Behaviour |
|---|---|
| mobile | Fields stacked; Re-run is full width |
| tablet / desktop | The two period fields are on one row |

### Accessibility

| Requirement | Implementation |
|---|---|
| ARIA role | Disclosure button + `<form>` with a `<fieldset>`/`<legend>` for the period range |
| Keyboard interaction | Enter/Space toggles; Enter in a field submits when valid |
| Label / aria-label | Visible labels; the allowed range is linked via `aria-describedby` |
| Contrast ratio | ≥ 4.5:1 text; error icon plus text |
| Screen reader | Errors are announced on blur |
| Focus management | Focus moves to the first field on expand; to the Hypothesis panel heading when the run starts |

---

## Flow-Level Transitions

| From | Action | To | Notes |
|------|--------|----|-------|
| Browser | Open card | Workspace (empty) | Focus on the `h1` |
| Browser | Featured CTA | Workspace (empty, featured) | Focus on [Analyze] (≤ 3 interactions to a result) |
| Workspace empty | Analyze | Loading, then success / partial / failed / paused | The live region announces each step |
| Success | Accept / Reject | Decided | The review status is stored by run ID |
| Success | Request more evidence | Loading, then success with "Added" items | On failure, the earlier result stays (AC5.4.3) |
| Success | Re-run | Loading, then a new run ID | Earlier decisions unchanged |
| Any | How accurate is this? | About & accuracy | Back returns to the same workspace state |

## Sources

- [wireframes] `ideation/rough-mockups/wireframes.md`
- [user-flow] `ideation/rough-mockups/user-flow.md`
- [stories] `inception/user-stories/stories.md`
- [requirements] `inception/requirements-analysis/requirements.md`
- [practices] `inception/practices-discovery/team-practices.md`
- [Q1]–[Q7] `inception/refined-mockups/refined-mockups-questions.md`

## Assumptions & Open Questions

- [assumption] The keyboard shortcuts for the chart (arrows, + / −, 0) are proposed here; confirm in functional design that they do not clash with screen-reader browse keys (they are active only while the chart has focus).
- The trace-step schema, parameter ranges, and step-update transport are set in design (from stories).
