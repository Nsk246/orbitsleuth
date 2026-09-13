# User Flow — OrbitSleuth

## Primary Flow: Investigate a Light Curve

```
Flow: Investigate a light curve
Persona: Visitor (hiring-manager/recruiter demo audience, or a casual public visitor)
Trigger: Visitor lands on the OrbitSleuth light-curve browser
Steps:
  1. [Light-Curve Browser] -> selects a light curve card (real or synthetic) -> [Investigation Workspace loads with the raw curve plotted]
  2. [Investigation Workspace, Empty state] -> clicks "Analyze" -> [system runs the tool-backed analysis pipeline]
  3. [Investigation Workspace, Loading state] -> (waits, sees progressive trace: fetch -> detrend -> period-detection -> vetting) -> [system returns a ranked hypothesis]
  4. [Investigation Workspace, Success state] -> reviews the hypothesis, confidence score, and cited evidence -> [visitor decides]
  5a. [Success state] -> clicks "Accept" -> [hypothesis marked accepted; recorded as the human-confirmed outcome]
  5b. [Success state] -> clicks "Reject" -> [hypothesis marked rejected; visitor may optionally annotate why]
  5c. [Success state] -> clicks "Annotate" -> [visitor adds a note without changing accept/reject state]
  5d. [Success state] -> clicks "Request more evidence" -> [system re-runs or extends analysis, returns to step 3 Loading state]
Success outcome: the visitor has an evidence-linked hypothesis with an explicit human accept/reject/annotate decision recorded — never an unreviewed AI verdict presented as final
Error paths:
  - Light curve fails to load -> [Investigation Workspace, Error state on chart] -> "This light curve could not be loaded" with a "Back to Browser" action
  - Analysis tool-chain step fails -> [Investigation Workspace, Error state on hypothesis panel] -> plain-language failure message + retry action; the trace panel shows which step failed
  - A vetting check cannot run (e.g. insufficient data) -> [Partial state] -> hypothesis still shown, with the missing check explicitly marked rather than silently dropped, and confidence reflecting the gap
```

## Secondary Flow: Browse and Filter

```
Flow: Browse and filter available light curves
Persona: Visitor
Trigger: Visitor wants to explore a specific kind of signal (e.g. only real archival data)
Steps:
  1. [Light-Curve Browser] -> selects a filter (Real data / Synthetic / All) -> [card grid updates to matching curves]
  2. [Light-Curve Browser, Partial state] -> (if few results) -> sees a smaller, non-stretched grid rather than an empty-looking page
Success outcome: visitor finds a light curve matching their interest and proceeds into the primary Investigate flow
Error paths:
  - Catalog fails to load -> [Light-Curve Browser, Error state] -> inline retry message, no raw error code
```

## Information Architecture

- **Depth**: 2 levels — Browser (root) and Investigation Workspace (per-curve). The observability trace is not a separate navigation level; it is an expandable section within the Workspace, consistent with keeping it secondary to the curve and hypothesis [Q3].
- **Navigation pattern**: hub-and-spoke — the Browser is the hub; each Investigation Workspace is reached from and returns to it via "Back to Browser" [Q1], [Q2].
- **Every screen reachable within 2 clicks** from the Browser (well within the 3-click guideline).

## Sources

[desc], [scope-document], [intent-backlog], `wireframes.md`, [Q1]-[Q6] per `rough-mockups-questions.md`.

## Assumptions & Open Questions

None.
