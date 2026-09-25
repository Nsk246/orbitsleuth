# Design System Mapping — OrbitSleuth

There is no existing brand or design system [rough-mockups Q4]. The UI uses
**headless accessible primitives** for behavior and **our own design tokens**
for the look [Q1]. The theme is **dark only** for the MVP. Components
reference semantic tokens only, never raw hex, so a light theme can be added
later by swapping one token set [Q2].

## Color Tokens (dark theme)

Contrast ratios were computed with the WCAG 2.1 relative-luminance formula.
Targets: ≥ 4.5:1 for text, ≥ 3:1 for UI components and graphics.

| Token | Value | On `bg.page` #0B1020 | On `bg.surface` #141B2E | Use |
|-------|-------|------------------|---------------------|-----|
| `bg.page` | #0B1020 | — | — | Page background |
| `bg.surface` | #141B2E | — | — | Cards, panels |
| `bg.chart` | #0F1628 | — | — | Chart plot area |
| `text.primary` | #E8ECF7 | 16.02 | 14.50 | Body text, headings |
| `text.secondary` | #A9B3CC | 9.02 | 8.16 | Labels, metadata |
| `color.accent` | #7CC4FF | 10.09 | 9.13 | Primary buttons (text `bg.page` on accent: 10.09), links |
| `color.focus` | #FFD166 | 13.13 | 11.88 | 2 px focus ring on every interactive element |
| `color.success` | #5FD39A | 10.16 | 9.19 | "Supports" icon, OK status (always with text) |
| `color.danger` | #FF7A85 | 7.55 | 6.83 | "Weakens" icon, FAILED status, errors (always with text) |
| `color.warning` | #FFB547 | 10.78 | 9.75 | SKIPPED / not run, paused banner (always with text) |
| `border.subtle` | #3A4766 | 2.05 | 1.85 | Decorative dividers only, never the only boundary of a control |
| `border.control` | #5A6A8F | 3.51 | 3.18 | Input, card, and toggle boundaries (≥ 3:1) |
| `series.raw` | #7CC4FF | 9.61 on `bg.chart` | — | Raw series: solid line |
| `series.detrended` | #C3A6FF | 8.77 on `bg.chart` | — | Detrended series: dotted line |
| `series.event` | #FFD166 | 12.49 on `bg.chart` | — | Folded event marker: triangle glyph |

Rule: every semantic color (success, danger, warning) comes with a text label
and an icon. Color never carries meaning on its own (WCAG 1.4.1).

## Typography, Spacing, Shape, Motion

| Token group | Values |
|-------------|--------|
| `font.family` | Sans-serif UI font; monospace for numeric values in evidence and trace |
| `font.size` | 12 (caption), 14 (body-sm), 16 (body), 20 (h3), 24 (h2), 32 (h1) px; body text never below 14 px |
| `space` | 4, 8, 16, 24, 32, 48 px (the uniform spacing scale) |
| `radius` | 4 (controls), 8 (cards) px |
| `motion.duration` | 150 ms (hover/press), 250 ms (disclosure); 0 under `prefers-reduced-motion` |
| `layout.maxWidth` | 1200 px |
| `target.min` | 44 × 44 px touch targets, 8 px between targets |

## Component → Primitive Mapping

The specific headless library is chosen in a later design stage. It must
provide the primitives below with WAI-ARIA Authoring Practices behavior.

| OrbitSleuth component | Headless primitive | Story / AC |
|-----------------------|--------------------|------------|
| CurveCard | Native link + button (no primitive needed) | US1.1, AC1.1.6 |
| FilterToggleGroup, chart view switch | Toggle group | US1.2, AC1.2.5; US2.2 |
| Provenance popover, TermHelp | Popover (non-modal) | US1.3, AC4.2.3 |
| LightCurveChart | Custom (chart library + wrapper); `role="img"` + description | US2.1–US2.3 |
| Ranked hypothesis rows, TracePanel, RerunDisclosure | Disclosure / collapsible | US4.1, US3.2, US5.3 |
| Change decision | Menu button | AC5.1.5 |
| NoteEditor, parameter fields | Native form controls + field/label primitive | US5.2, US5.3 |
| Status announcements | Live-region utility (polite) | AC3.2.3, AC5.1.2, AC1.2.5 |
| Metrics table (About) | Native `<table>` | AC4.1.4 |
| Skeletons | Custom, `aria-hidden` + live-region text | AC1.1.4 |

## Responsive Rules

| Breakpoint | Grid | Notes |
|------------|------|-------|
| mobile < 768 px | 1 column, 16 px gutters | Reflow with no horizontal scroll at 320 px; stacked Workspace |
| tablet 768–1023 px | 2 columns, 24 px gutters | Browser grid 2-up; Workspace chart full width |
| desktop ≥ 1024 px | 12-column grid, 24 px gutters | Browser 4-up; Workspace 60/40 split |

## Sources

- [rough-mockups] `ideation/rough-mockups/wireframes.md`, `rough-mockups-questions.md` (Q4 dark theme, Q5 responsive, Q6 WCAG)
- [stories] `inception/user-stories/stories.md`
- [requirements] `inception/requirements-analysis/requirements.md` (NFR3, NFR4)
- [Q1]–[Q2] `inception/refined-mockups/refined-mockups-questions.md`

## Assumptions & Open Questions

- [assumption] The UI font and chart library are chosen alongside the frontend framework in a later design stage; token values stay valid for any choice.
- [assumption] Hex values are a starting palette; the contrast ratios above must be re-checked if any value changes.
