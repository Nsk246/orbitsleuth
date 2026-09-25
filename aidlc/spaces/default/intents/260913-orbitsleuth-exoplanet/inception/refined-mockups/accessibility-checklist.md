# Accessibility Checklist — OrbitSleuth (WCAG 2.1 AA)

Target: WCAG 2.1 Level AA on all four views [rough-mockups Q6] [NFR3]. Each
row names the design decision that meets the criterion and the story AC or
test that checks it.

## Perceivable

| WCAG criterion | Design decision | Verified by |
|----------------|-----------------|-------------|
| 1.1.1 Non-text content | The chart has `role="img"` and a live text summary; thumbnails are `aria-hidden`, and cards carry the name | AC2.1.3, AC1.1.6 |
| 1.3.1 Info and relationships | Landmarks (header, main, footer); `h1` per view; `<ol>` for the ranked list and trace; `<table>` for metrics; fieldset/legend for the period range | axe scan AC1.1.7 |
| 1.3.2 Meaningful sequence | DOM order matches the visual order at every breakpoint (chart → hypothesis → trace) | Keyboard walk-through |
| 1.4.1 Use of color | Statuses are text plus icon; series differ by line style; scores are numbers | AC2.2.3, AC3.2.3, AC4.1.2, AC4.2.1 |
| 1.4.3 Contrast (minimum) | All text tokens ≥ 7.5:1 on page and surface backgrounds | `design-system-mapping.md` table; AC4.1.5 |
| 1.4.10 Reflow | Single column with no horizontal scroll at 320 px | AC2.1.6, AC1.1.3 |
| 1.4.11 Non-text contrast | `border.control` ≥ 3:1; focus ring ≥ 11:1; plot lines ≥ 8:1 | AC1.1.6, AC2.2.3 |
| 1.4.12 Text spacing | Layouts use relative units; no fixed-height text containers | Component tests with the text-spacing bookmarklet |
| 1.4.13 Content on hover or focus | Popovers are click/Enter triggered, dismissible with Escape, and persistent until closed | AC4.2.3 |

## Operable

| WCAG criterion | Design decision | Verified by |
|----------------|-----------------|-------------|
| 2.1.1 Keyboard | Every action is reachable: cards, filter, chart pan/zoom keys, view switch, review actions, popovers, disclosures | AC2.1.3, AC5.1.4, keyboard e2e |
| 2.1.2 No keyboard trap | Popovers are non-modal; no modals in the MVP | Keyboard walk-through |
| 2.4.1 Bypass blocks | A skip-to-content link comes first on every view | axe scan |
| 2.4.3 Focus order | Focus moves to the Workspace `h1` on open and returns to the originating card on Back; defined focus targets after decisions, notes, and re-runs | AC2.1.5, interaction-spec focus rows |
| 2.4.6 Headings and labels | Descriptive `h1` (target ID), `h2`/`h3` per panel; visible labels on all fields | AC5.2.4 |
| 2.4.7 Focus visible | A 2 px `color.focus` ring on every interactive element | AC1.1.6 |
| 2.5.1 Pointer gestures | Pinch/drag always has zoom and pan buttons as a fallback | AC2.1.7 |
| 2.5.3 Label in name | Accessible names begin with the visible text ("Accept transit hypothesis") | axe scan |
| 2.2.2 Pause, stop, hide | The trace replay respects reduced motion; no auto-moving content beyond step reveals | AC3.2.3 |
| Target size (AAA 2.5.5, adopted) | 44 × 44 px minimum on touch targets | Component tests |

## Understandable

| WCAG criterion | Design decision | Verified by |
|----------------|-----------------|-------------|
| 3.1.1 Language of page | `lang="en"` | axe scan |
| 3.2.1 / 3.2.2 On focus / on input | The filter changes results but not the context; nothing navigates on focus | Keyboard walk-through |
| 3.2.3 Consistent navigation | The same header on every view | Visual review |
| 3.3.1 Error identification | Note-length and parameter errors are shown in text, linked with `aria-describedby`, and announced | AC5.2.4, AC5.3.3 |
| 3.3.2 Labels or instructions | Allowed ranges are shown before input; the character counter is always visible | AC5.2.4 |
| 3.3.4 Error prevention | Decisions are reversible (Change decision/Clear); Analyze is disabled during a run | AC5.1.5, AC3.1.4 |
| Plain language (supporting) | "?" definitions for 7 technical terms; the ranking score is explained in plain words | AC4.2.3, AC4.1.2 |

## Robust

| WCAG criterion | Design decision | Verified by |
|----------------|-----------------|-------------|
| 4.1.2 Name, role, value | Native elements first; headless primitives for toggle group, popover, disclosure, and menu button, with `aria-pressed` / `aria-expanded` state | axe scan AC1.1.7 |
| 4.1.3 Status messages | One polite live region per view: catalog count, run steps, decisions, paused notices | AC1.2.5, AC3.2.3, AC5.1.2 |

## Test Plan Summary

1. **Automated**: an axe-core scan of the Browser, Workspace (each panel
   state), and About views reports zero AA violations (AC1.1.7). It runs in
   CI component tests.
2. **Keyboard**: a scripted e2e covers the core flow with the keyboard only,
   as part of smoke test 1.
3. **Screen reader**: before each demo release, a manual pass with VoiceOver
   and NVDA over the core flow.
4. **Zoom and reflow**: manual checks at 200% and 400% zoom and at 320 px
   width.
5. **Color vision**: a manual check with simulated protanopia,
   deuteranopia, and tritanopia (the design already avoids color-only
   meaning).

## Sources

- [rough-mockups] `ideation/rough-mockups/wireframes.md`, `rough-mockups-questions.md` (Q6)
- [stories] `inception/user-stories/stories.md`
- [requirements] `inception/requirements-analysis/requirements.md` (NFR3, NFR4)
- `inception/refined-mockups/design-system-mapping.md`, `interaction-spec.md`

## Assumptions & Open Questions

- [assumption] Target size 44 × 44 px is adopted from AAA 2.5.5 as a design choice; AA itself does not require it.
- Manual screen-reader passes are a release checklist item, not an automated gate.
