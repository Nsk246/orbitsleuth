# Wireframes — OrbitSleuth

Low-fidelity concept wireframes for the confirmed 3-view single-page app
[Q1]. Visual style: fresh, space/astronomy-inspired dark theme with clean
data-visualization styling [Q4]. Full responsive support (desktop, tablet,
mobile) [Q5]. WCAG 2.1 AA baseline throughout [Q6].

## View 1: Light-Curve Browser

```
+--------------------------------------------------------------+
| OrbitSleuth                                    [nav] [about] |  <- h1 "OrbitSleuth", header landmark
+--------------------------------------------------------------+
| Filter: [ Real data ] [ Synthetic ] [ All ]                  |
+--------------------------------------------------------------+
| +----------+  +----------+  +----------+  +----------+       |
| | curve    |  | curve    |  | curve    |  | curve    |       |  <- main landmark, card layout
| | thumb A  |  | thumb B  |  | thumb C  |  | thumb D  |       |
| | KOI-1234 |  | KOI-5678 |  | synth-01 |  | KOI-9012 |       |
| +----------+  +----------+  +----------+  +----------+       |
|      ... more cards ...                                      |
+--------------------------------------------------------------+
```

Accessibility note: `h1` = "OrbitSleuth" in header landmark; cards are a
`nav`-free grid inside `main`; each card is a focusable link/button
(keyboard entry point) labeled with the target id (e.g. "Open KOI-1234").

### States

| State | Description |
|-------|-------------|
| Empty | First load with no data source configured yet — shows a message directing to select a data filter, no blank grid |
| Loading | Skeleton cards (per wireframing-guide.md) while the curve catalog loads |
| Success | Populated card grid (shown above) |
| Error | Data source unavailable — inline message with retry action, no raw error code |
| Partial | Fewer than a full row of cards (e.g. 2 results after filtering) — grid does not stretch to fill, cards stay their natural size |

### Responsive Behaviour

| Breakpoint | Behaviour |
|------------|-----------|
| Mobile (<768px) | Single-column card list |
| Tablet (768-1023px) | 2-column card grid |
| Desktop (1024px+) | 4-column card grid |

## View 2: Investigation Workspace

```
+--------------------------------------------------------------+
| < Back to Browser        KOI-1234                             |  <- h1 = curve id, header landmark
+----------------------------------------+---------------------+
|                                        | Hypothesis           |  <- main landmark
|   [ Light curve chart: raw ]          | -------------------- |
|   [ toggle: raw | detrended ]         | Transit (72% conf.)  |
|   (zoom / pan controls)               |                       |
|                                        | Evidence:             |
|                                        |  - Depth: 0.021       |
|                                        |  - Period: 3.2d       |
|                                        |  - Odd/even: match    |
|                                        |                       |
|                                        | [Accept] [Reject]     |
|                                        | [Annotate] [Re-run]   |
|                                        | [Request more evid.]  |
+----------------------------------------+---------------------+
| > Analysis trace (expandable panel)                            |  <- observability, secondary/expandable per Q3
+--------------------------------------------------------------+
```

Accessibility note: `h1` = the curve id; chart region has an `aria-label`
summarizing the plotted data (e.g. "Light curve for KOI-1234, raw view");
the trace panel is a collapsible `region` with `aria-expanded`; action
buttons are native `<button>` elements, each independently
keyboard-focusable and labeled.

### States

| State | Description |
|-------|-------------|
| Empty | Curve loaded, analysis not yet run — hypothesis panel shows "Run analysis to see results" with a prominent "Analyze" call to action |
| Loading | Analysis in progress — hypothesis panel shows a progress indicator naming the running step (e.g. "Detrending...", "Running vetting checks...") rather than a bare spinner, consistent with observability |
| Success | Populated hypothesis + evidence + confidence (shown above) |
| Error | Analysis tool failure — inline message in the hypothesis panel, plain language, with a retry action; raw tool errors never shown to the user |
| Partial | Hypothesis returned with low confidence or incomplete evidence (e.g. one vetting check could not run) — evidence list explicitly marks the missing check rather than omitting it silently |

### Responsive Behaviour

| Breakpoint | Behaviour |
|------------|-----------|
| Mobile (<768px) | Chart stacked above hypothesis panel; trace panel becomes a full-width expandable section at the bottom |
| Tablet (768-1023px) | Chart and hypothesis panel stacked or side-by-side depending on available width; trace panel full-width below |
| Desktop (1024px+) | Chart and hypothesis panel side-by-side (shown above); trace panel full-width below both |

## View 3: Analysis Trace / Observability Panel

Rendered as the expandable panel at the bottom of the Investigation
Workspace (View 2), not a separate route — consistent with keeping
evidence/observability secondary to the curve and hypothesis [Q3].

```
+--------------------------------------------------------------+
| v Analysis trace                                               |
+--------------------------------------------------------------+
| 1. Fetched light curve data ............................ OK  |
| 2. Detrending ........................................... OK  |
| 3. Period-detection (box-least-squares) ................ OK  |
| 4. Vetting: odd/even depth comparison ................... OK |
| 5. Vetting: secondary eclipse check ................ SKIPPED |
| Evaluation: this signal type present in NASA KOI reference set|
+--------------------------------------------------------------+
```

Accessibility note: an ordered list (`<ol>`) of tool-chain steps, each with
a text status (not color-only); the panel uses `aria-live="polite"` while
steps are updating during analysis so screen-reader users hear progress.

### States

| State | Description |
|-------|-------------|
| Empty | No analysis run yet — panel collapsed and disabled with a note "Run an analysis to see its trace" |
| Loading | Steps populate progressively as the tool-chain runs |
| Success | Full step list with statuses (shown above) |
| Error | A step failed — that step shows "FAILED" with a plain-language reason, and later steps show "not run" rather than being silently omitted |
| Partial | Some steps SKIPPED (as shown above) rather than run — always explicit, never silently absent |

## Sources

[desc], [scope-document], [intent-backlog], [Q1]-[Q6] per `rough-mockups-questions.md`.

## Assumptions & Open Questions

None.
