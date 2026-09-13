# Rough Mockups & Concept Visualization — Questions

## Sources

- [desc] Initial description: "Plan OrbitSleuth, a resume-ready interactive AI exoplanet investigation platform. Users explore telescope light curves, investigate unusual signals with tool-backed AI analysis, and receive evidence-linked hypotheses such as exoplanet transit, stellar activity, or noise. The product must never claim a discovery without supporting evidence and must keep a human reviewer in control. Begin with AI-DLC discovery and planning only: do not generate application code, provision cloud resources, or create a deployment plan until I approve the Unit of Work. The target outcome is a visually compelling web experience with a Python analysis backend, an interactive frontend, evaluation, observability, and a future AWS deployment path."
- [scope-document] `ideation/scope-definition/scope-document.md`: MVP loop = browse/select light curve → analyze → ranked hypothesis with evidence+confidence → human accept/reject/annotate; must-have observability trace; won't-have accounts/collaboration/reports.
- [intent-backlog] `ideation/scope-definition/intent-backlog.md`: 8 Must Have proto-Units (ingestion, analysis engine, hypothesis generation, viewer, hypothesis display, human review workflow, observability trace, evaluation methodology).

## Q1. What are the primary entry points and key screens/views?

- A. A single-page app with 3 key views: (1) a light-curve browser/gallery, (2) an investigation workspace (viewer + analysis + hypothesis + review, likely as one combined screen or a split view), (3) an evidence/observability trace panel (could be part of the investigation workspace, e.g. a side panel or expandable section)
- B. A different set of key screens (please specify in Other)
- C. Not yet defined
- [Answer]: A

## Q2. What is the core user flow (happy path) end-to-end?

- A. Land on the light-curve browser → pick a light curve (real or synthetic) → land on the investigation workspace showing the curve → click "Analyze" → see a loading/progress state while tools run → see the ranked hypothesis with evidence + confidence → accept, reject, annotate, or request more evidence → (if requested) see updated results
- B. A different flow (please specify in Other)
- C. Not yet defined
- [Answer]: A

## Q3. What does the information hierarchy look like — what should be most visually prominent?

- A. The light curve visualization and the resulting hypothesis + confidence are the most prominent; the supporting evidence trace and observability detail are secondary/expandable, not competing for primary visual attention
- B. A different hierarchy (please specify in Other)
- C. Not yet defined
- [Answer]: A

## Q4. Are there existing brand guidelines, design systems, or UI patterns to follow?

- A. None yet — this is a fresh visual identity; a space/astronomy-inspired dark theme with clean data-visualization styling would fit the "visually compelling" goal, but there is no locked brand system yet
- B. A specific existing design system or style should be followed (please specify in Other)
- C. Not applicable
- [Answer]: A

## Q5. What device/form factors must be supported?

- A. Desktop-first (this is a data-dense investigation tool, primarily demoed on desktop/laptop), with the layout still working reasonably on tablet; deep mobile optimization is not a priority for the MVP
- B. Full responsive support across desktop, tablet, and mobile is required
- C. Desktop-only; mobile/tablet are explicitly out of scope
- D. Not yet defined
- [Answer]: B. Full responsive support across desktop, tablet, and mobile is required

## Q6. Are there known accessibility requirements (WCAG level, screen reader support, keyboard-only navigation)?

- A. Target WCAG 2.1 AA as a baseline good practice (per the design agent's standard), with a general expectation of keyboard operability and reasonable screen-reader labeling, but no formal/legal accessibility audit requirement for this solo project
- B. A stricter formal requirement applies (please specify in Other)
- C. Not a priority for this MVP
- D. Not yet defined
- [Answer]: A

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Looks correct
- Request changes

[Answer]: Looks correct
