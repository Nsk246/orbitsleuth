# Refined Mockups — OrbitSleuth

These are mid-fidelity mockups for the three confirmed views: the Light-Curve
Browser, the Investigation Workspace, and the Analysis Trace panel
[wireframes]. They add a fourth view, About & accuracy [Q5]. The visual
language is a dark, space-inspired theme built from our own design tokens on
headless accessible primitives [Q1] [Q2]. Token values are in
`design-system-mapping.md`. Component behavior is in `interaction-spec.md`.

Global layout on every view:

- A skip-to-content link comes first.
- The header holds the "OrbitSleuth" logo (a link to the Browser) and an
  "About & accuracy" link.
- `main` holds the view.
- The footer holds the pipeline version and a repository link.
- Content is capped at 1200 px wide on large screens.

## View 1 — Light-Curve Browser (hub)

Stories: US1.1, US1.2, US1.3, US1.4.

```
+----------------------------------------------------------------------+
| [Skip to content]                                                    |
| ✦ OrbitSleuth                                     About & accuracy   |  header
+----------------------------------------------------------------------+
| Investigate real telescope light curves with tool-backed analysis    |  one-line pitch (AC1.4.1)
| — you make the final call.        [ ▶ Start with a featured example ]|  primary CTA
+----------------------------------------------------------------------+
| Show:  ( Real ) ( Synthetic ) (•All )            32 light curves     |  toggle group + live count
+----------------------------------------------------------------------+
| +--------------+ +--------------+ +--------------+ +--------------+  |
| | ~~~\_/~~~~   | | ~~\/~~~\/~~  | | ~~~~~~~~~~~  | | ~\__/~~~~~   |  |  thumbnails (sparkline)
| | KOI-1234     | | synth-07     | | KOI-5678     | | KOI-9012     |  |  target ID (h2)
| | ● Real·Kepler| | ◆ Synthetic  | | ● Real·Kepler| | ● Real·TESS  |  |  type badge: icon + text
| | [ⓘ Source ]  | | [ⓘ Source ]  | | [ⓘ Source ]  | | [ⓘ Source ]  |  |  provenance (US1.3)
| +--------------+ +--------------+ +--------------+ +--------------+  |
|   ... more cards ...                                                 |
+----------------------------------------------------------------------+
| Pipeline v0.x · Source on GitHub                                     |  footer
+----------------------------------------------------------------------+
```

Content notes:

- The whole card is one link, named "Open KOI-1234, real data" (AC1.1.6).
  The "Source" control is a separate button inside the card area, placed
  after the card link in tab order. It opens a provenance popover
  (AC1.3.1, AC1.3.2).
- The featured example is marked on its card with a "★ Featured" text badge.
  The CTA goes straight to its workspace with focus on "Analyze" (AC1.4.2).
- The filter labels are "Real", "Synthetic", and "All", matching FR1.3
  (AC1.2.1).

### States

| State | Mockup |
|-------|--------|
| Loading | 8 skeleton cards in the grid shape; live region says "Loading light curves" (AC1.1.4) |
| Success | As shown above |
| Empty catalog | Telescope icon + "No light curves are available right now." + "Retry" (AC1.1.5) |
| Zero filter matches | "No light curves match this filter." + [Show all] (AC1.2.4) |
| Partial (few matches) | Cards keep their full-row width; the grid does not stretch (AC1.2.3) |
| Error | Inline panel: "We couldn't load the light curves. Check your connection and try again." + [Retry]; no error code (AC1.1.2) |

### Responsive

| Breakpoint | Layout |
|------------|--------|
| < 768 px | One-column card list; the pitch and CTA stack, and the CTA is full-width; the filter wraps below the count |
| 768–1023 px | Two-column grid |
| ≥ 1024 px | Four-column grid |

## View 2 — Investigation Workspace (spoke)

Stories: US2.1–US2.3, US3.1–US3.5, US4.1–US4.5, US5.1–US5.5.

```
+----------------------------------------------------------------------+
| ✦ OrbitSleuth                                     About & accuracy   |
+----------------------------------------------------------------------+
| ← Back to Browser                                                    |
| KOI-1234  ● Real · Kepler                                    (h1)    |
+-----------------------------------------+----------------------------+
| View: (•Raw) ( Detrended ) ( Folded )   | HYPOTHESIS                 |
|  [+][-][Reset]                          | +------------------------+ |
|  ┌───────────────────────────────────┐  | | ★ Transit          72  | |  top card (Q3)
|  │ ····.·····.····v····.·····v·····  │  | | "consistent with a     | |
|  │ ······················(flux)····· │  | |  transiting planet"    | |
|  └───────────────────────────────────┘  | | Ranking score ⓘ        | |
|   time (days) →                         | | Status: UNREVIEWED     | |
|   Chart summary (sr-only): KOI-1234,    | | [Accept] [Reject]      | |
|   raw, 65,210 points, day 0–90...       | | [Add note]             | |
|                                         | +------------------------+ |
|                                         | Other explanations         |
|                                         |  Eclipsing binary  ▇▇▇ 41 ▸|
|                                         |  Stellar activity  ▇▇  22 ▸|
|                                         |  Noise             ▇   9  ▸|
|                                         |                            |
|                                         | EVIDENCE — Transit         |
|                                         |  Supports                  |
|                                         |   ✓ Depth 0.021 (BLS) ⓘ    |
|                                         |   ✓ Odd/even match ⓘ       |
|                                         |  Weakens                   |
|                                         |   ✗ SNR 7.2, near limit ⓘ  |
|                                         |  Not run                   |
|                                         |   – Secondary eclipse:     |
|                                         |     too few transits       |
|                                         |                            |
|                                         | Explanation                |
|                                         |  "The light dips by about  |
|                                         |   2% every 3.2 days..."    |
|                                         |                            |
|                                         | [Request more evidence]    |
|                                         | ▸ Adjust and re-run        |
|                                         | How accurate is this? →    |
+-----------------------------------------+----------------------------+
| ▸ Analysis trace (5 steps)                                   View 3  |
+----------------------------------------------------------------------+
```

Content notes:

- **Scores**: every score is a number with a text label (for example "72"
  plus "Ranking score"). The "?" next to "Ranking score" defines it as "how
  this compares with the other explanations, not the chance it is a planet"
  (AC4.1.2).
- **Evidence icons**: ✓, ✗, and – always come with their group heading
  text, so meaning never relies on the icon or color alone (AC4.2.1).
- **Folded view**: shown only when a period exists. Otherwise it is
  `aria-disabled` with the visible hint "Available after a period is
  detected" (AC2.3.2).
- **Status after a decision**: the badge reads "ACCEPTED" or "REJECTED",
  and the buttons become [Change decision ▾] with the options Accept,
  Reject, and Clear (AC5.1.5).
- **Advisory wording**: labels never say "discovery" or "confirmed planet"
  (AC4.4.1).

### States

| State | Hypothesis panel | Chart | Trace |
|-------|------------------|-------|-------|
| Empty (not analyzed) | "Run analysis to see results" + primary [Analyze] (AC3.1.3) | Raw curve | Collapsed, disabled: "Run an analysis to see its trace" |
| Loading | Names the current step: "Detrending…" with a step counter "2 of 5"; [Analyze] is disabled (AC3.1.4) | Raw curve | Steps appear one by one (AC3.2.1) |
| Success | As shown above | All views available | All steps OK |
| Partial | Same as success, plus a notice: "1 check did not run — confidence reduced by 8 points" (AC3.3.2) | — | That step is SKIPPED, with its reason |
| Low confidence (valid) | Normal result layout; the top card may show a low score; no error styling (AC3.4.3) | — | All OK |
| Analysis failed | Error card: "The analysis couldn't finish: the period search failed. This is a pipeline error, not a 'nothing found' result." + [Retry] (AC3.4.2) | Raw curve stays | Failed step FAILED; later steps "not run" |
| Live runs paused | Cached result, plus an info banner: "Live analysis is paused (hourly limit reached). Showing the saved result." (AC3.5.1) | — | Replay of the saved trace |
| Paused, no cache | "Live analysis is paused. It resumes at 15:00 UTC." (AC3.5.2) | — | — |
| Curve load failed | — | "This light curve could not be loaded." + [Back to Browser] (AC2.1.4) | Hidden |
| Explanation unavailable | The explanation block reads "Explanation unavailable — the results and evidence above are complete." (AC4.3.2) | — | Explanation step SKIPPED |

### Responsive

| Breakpoint | Layout |
|------------|--------|
| < 768 px | Order: header, then chart (full width, zoom buttons visible), then the hypothesis panel, then the trace. No horizontal scroll at 320 px (AC2.1.6). Pinch and drag zoom and pan (AC2.1.7). |
| 768–1023 px | Chart full width; the hypothesis panel below it in two columns (the top card and other explanations, then evidence); trace below |
| ≥ 1024 px | Chart about 60% and panel about 40% side by side; trace full width below |

### Collapsed "Adjust and re-run" (Q7)

```
▾ Adjust and re-run
  Detrending window (hours)   [ 12 ]   allowed 2–48
  Period search range (days)  [ 0.5 ] to [ 30 ]   allowed 0.3–100
  [Re-run analysis]
```

The ranges shown are placeholders. Functional design sets the real values
(AC5.3.3).

## View 3 — Analysis Trace panel

Stories: US3.1, US3.2, US3.4, US4.3.

```
+----------------------------------------------------------------------+
| ▾ Analysis trace · run 7f3a… · pipeline v0.x                          |
+----------------------------------------------------------------------+
| 1. Load light curve ........ OK        0.2 s   65,210 points          |
| 2. Detrend ................. OK        1.1 s   window 12 h            |
| 3. Period search (BLS) ..... OK        4.8 s   P = 3.21 d             |
| 4. Vetting: odd/even ....... OK        0.3 s   ratio 1.02             |
| 5. Vetting: secondary ...... SKIPPED   —       too few transits      |
| 6. Explanation ............. OK        1.4 s   used steps 3, 4        |
+----------------------------------------------------------------------+
```

- The trace is an ordered list. Each status is text. Updates go to a polite
  live region. With reduced motion, the replay shows steps without
  animation (AC3.2.3).
- **Interrupted run**: the banner reads "Connection lost — the run was
  interrupted." with [Retry] (AC3.2.4).

## View 4 — About & accuracy (Q5)

Stories: US4.1 (AC4.1.4), US6.5.

```
+----------------------------------------------------------------------+
| ✦ OrbitSleuth                                     About & accuracy   |
+----------------------------------------------------------------------+
| About OrbitSleuth                                             (h1)   |
| One paragraph: what it does, the tool-backed approach, and that      |
| humans make the final call.                                          |
|                                                                      |
| How accurate is it?                                           (h2)   |
| Evaluated on 150 KOI targets · pipeline v0.x · 2026-10-01            |
| +-------------------+----------+-----------+--------+                |
| | Category          | Precision| Recall    | Count  |                |
| +-------------------+----------+-----------+--------+                |
| | Transit           |   0.81   |   0.74    |  62    |                |
| | Eclipsing binary  |   0.69   |   0.77    |  41    |                |
| | ...               |          |           |        |                |
| | Unmapped          |    —     |    —      |   6    |                |
| +-------------------+----------+-----------+--------+                |
| Overall accuracy: 0.xx                                               |
| What these numbers mean ⓘ · Method and sample (repository link)      |
+----------------------------------------------------------------------+
```

The numbers above are illustrative placeholders, not results. The table is
a real `<table>` with a caption and column headers. It becomes stacked cards
below 768 px.

## Story Coverage

| Story group | View | Covered stories |
|-------------|------|-----------------|
| 1 Browse | View 1 | US1.1, US1.2, US1.3, US1.4 |
| 2 View curve | View 2 chart | US2.1, US2.2, US2.3 |
| 3 Analyze | View 2 panel + View 3 | US3.1, US3.2, US3.3, US3.4, US3.5 |
| 4 Review | View 2 panel + View 4 | US4.1, US4.2, US4.3, US4.4, US4.5 |
| 5 Act | View 2 panel | US5.1, US5.2, US5.3, US5.4, US5.5 |
| 0, 6 Foundations / Operator | No UI (US6.5 surfaces in View 4) | US6.5 (display only) |

US4.5 needs no separate screen. The eclipsing-binary hypothesis uses the same
top-card and ranked-list pattern, and its triggering check is cited in the
evidence groups.

## Sources

- [wireframes] `ideation/rough-mockups/wireframes.md`
- [user-flow] `ideation/rough-mockups/user-flow.md`
- [stories] `inception/user-stories/stories.md`
- [requirements] `inception/requirements-analysis/requirements.md`
- [Q1]–[Q7] `inception/refined-mockups/refined-mockups-questions.md`

## Assumptions & Open Questions

- [assumption] Parameter ranges, the confidence-penalty size, and the resume time shown in the mockups are placeholders; functional design sets them.
- [assumption] The metric values on the About & accuracy view are illustrative only.
- Open: the frontend framework and headless primitive library are chosen in a later design stage.
