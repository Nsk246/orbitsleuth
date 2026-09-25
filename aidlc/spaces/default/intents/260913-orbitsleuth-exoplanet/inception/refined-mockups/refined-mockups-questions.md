# Refined Mockups — Clarifying Questions

## Sources

- [wireframes] `ideation/rough-mockups/wireframes.md` (three views, five states each, dark space theme, WCAG 2.1 AA, three breakpoints)
- [user-flow] `ideation/rough-mockups/user-flow.md`
- [stories] `inception/user-stories/stories.md` (36 stories; screen-state, accessibility, and responsive ACs)
- [requirements] `inception/requirements-analysis/requirements.md`

Already settled and not re-asked: the three views, hub-and-spoke navigation,
the five screen states, full responsive support at three breakpoints, WCAG
2.1 AA, a fresh dark space-inspired theme with no existing brand system
[rough-mockups Q1–Q6], and the story-level behaviors.

## Q1. What should the UI components be built on?

There is no existing design system [rough-mockups Q4]. This choice affects
accessibility effort and how distinct the look can be. The frontend framework
itself is chosen in a later design stage.

- A. Accessible unstyled ("headless") component primitives plus our own design tokens for the dark space theme: accessibility behavior comes built in, and the visual identity is fully ours
- B. A full pre-styled component kit, re-themed dark: fastest to build, but looks more generic
- C. Hand-built components from scratch: full control, but every accessibility behavior must be built and tested by hand
- X. Other (please specify)

[Answer]: A (2026-09-25T14:16:22Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q2. Dark theme only, or dark plus light?

The confirmed style is dark and space-inspired. A light option helps readers
in bright rooms and some low-vision users, but doubles the contrast checks.

- A. Dark only for the MVP; the design tokens are structured so a light theme can be added later
- B. Dark and light, with a toggle that follows the system setting by default
- X. Other (please specify)

[Answer]: A (2026-09-25T14:16:22Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q3. How should the four ranked hypotheses be shown?

Every analysis returns all four categories (transit, eclipsing binary,
stellar activity, noise), ranked by score (AC0.6.1, AC4.1.1).

- A. A prominent card for the top hypothesis, then the other three as a compact ranked list with score bars and numbers; each can expand to show its evidence
- B. Four equal cards side by side (stacked on mobile)
- C. Only the top hypothesis, with an "Other explanations" section collapsed by default
- X. Other (please specify)

[Answer]: A (2026-09-25T14:16:22Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q4. How should the evidence items be laid out?

Each item shows the tool, the measured value, and whether it supports or
weakens the hypothesis (AC4.2.1).

- A. Two short groups, "Supports" and "Weakens", each a list of items with value, tool name, and a term-definition control
- B. One table with columns Check / Value / Effect / Tool, sortable
- X. Other (please specify)

[Answer]: A (2026-09-25T14:16:22Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q5. Where does "How accurate is this?" lead?

The requirements review left this location open (FR8.6, review finding R-02).
A dedicated evaluation screen is out of scope.

- A. An "About & accuracy" page reached from the header's About link and from the workspace link: a one-paragraph project summary, the latest metrics table, the pipeline version, and the date
- B. A side panel that slides over the workspace with the metrics table
- C. A link to the evaluation report file in the public repository
- X. Other (please specify)

[Answer]: A (2026-09-25T14:16:22Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q6. How are technical terms explained?

Each term has a fixed plain-language definition (AC4.2.3) that must work
with mouse, keyboard, and touch.

- A. A small "?" button next to the term that opens a short popover; Escape or a second click closes it
- B. Terms link to a glossary section on the About page
- X. Other (please specify)

[Answer]: A (2026-09-25T14:16:22Z, **Mode:** chat — user accepted the recommended option: "all good")

## Q7. Where do the re-run parameters live?

The reviewer can change the detrending window and period range before a
re-run (US5.3). Most visitors will not touch them.

- A. Inside a collapsed "Adjust and re-run" section under the hypothesis panel (progressive disclosure)
- B. In a modal dialog opened by the "Re-run" button
- X. Other (please specify)

[Answer]: A (2026-09-25T14:16:22Z, **Mode:** chat — user accepted the recommended option: "all good")

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Components: headless accessible primitives plus our own dark-theme design tokens (Q1: A)
- Theme: dark only for the MVP; tokens structured so a light theme can be added later (Q2: A)
- Hypotheses: a prominent top card, then the other three as a compact ranked list with score bars and numbers, each expandable (Q3: A)
- Evidence: "Supports" and "Weakens" groups, each item with value, tool, and a term-definition control (Q4: A)
- "How accurate is this?" leads to an "About & accuracy" page reached from the header and the workspace (Q5: A)
- Technical terms: "?" popover buttons, closed with Escape or a second click (Q6: A)
- Re-run parameters: a collapsed "Adjust and re-run" section under the hypothesis panel (Q7: A)

Does this all look correct before I generate the refined mockups?

- Looks correct
- Request changes

[Answer]: Looks correct
