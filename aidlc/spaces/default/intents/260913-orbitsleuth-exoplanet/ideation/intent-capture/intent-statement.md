# Intent Statement — OrbitSleuth

## Problem Statement

OrbitSleuth exists to demonstrate end-to-end AI-assisted scientific
investigation skill as a portfolio/resume project, rather than to solve a
production business need [Q1]. The core demonstration is a rigorous,
tool-backed AI analysis workflow applied to a real scientific domain
(exoplanet light-curve investigation), built with the discipline of a
production system even though the driving purpose is career/opportunity
[Q4].

## Target Customer

The primary audience is the author's own demonstration of applied-AI and
full-stack skill to prospective hiring managers and recruiters [Q2]. The
product is also intended to be usable by general members of the public who
find it online — "normal fellow visitors" browsing the deployed site — even
though they are not the primary audience the project is built to impress
[Q2].

## Success Metrics

Success is defined on two dimensions together, not either alone [Q3]:
- A polished, demoable product: a genuinely interactive, modern, smooth and
  responsive frontend that showcases UX and engineering craft to reviewers
  and employers [Q3].
- Measurable analytical rigor: the AI's hypotheses are evaluated for
  accuracy (e.g., against known/labeled signals), not just visual polish
  [Q3].

## Initiative Trigger

The initiative is triggered by a career/opportunity motivation: building a
standout AI-era resume project [Q4].

## Initial Scope Signal

- **Workflow-selected scope**: `mvp` [scope].
- **User-confirmed product boundary**: The user confirmed the `mvp` scope
  matches their intended boundary — the core interactive investigation
  experience, evaluation, and observability, without a full
  production-grade deployment build-out at this stage [Q8]. Per the
  original request, discovery and planning proceed now; application code,
  cloud provisioning, and a deployment plan wait for explicit Unit of Work
  approval [desc].

## Product Behavior Commitments

These commitments were explicitly confirmed and constrain every downstream
design and build decision:

- **Evidence-linked hypotheses only**: every hypothesis the AI proposes
  (e.g., exoplanet transit, stellar activity, noise) must cite the specific
  evidence supporting it (e.g., transit depth, periodicity, odd/even
  mismatch, vetting-tool output) and must state a confidence/uncertainty
  level rather than issuing a bare verdict [Q9]. The AI's role is advisory;
  it never unilaterally declares a "discovery" [desc].
- **Human reviewer in control**: every AI-generated hypothesis requires an
  explicit human accept/reject/annotate action before it is treated as
  final, and the human can also intervene at any point in the
  investigation — re-run analysis, adjust parameters, or request more
  evidence — rather than only approving or rejecting a finished verdict
  [Q10].
- **Tool-backed, not free-form, analysis**: the AI's investigation is
  backed by real signal-processing/astronomy analysis tools (e.g.,
  period-detection / box-least-squares-style transit search, detrending,
  vetting statistics) that the AI calls and interprets, rather than
  reasoning freely without invoking analysis tools [Q12].
- **Data sources**: the platform uses both public archival light-curve data
  (e.g., NASA Kepler/TESS/K2-style sources) and synthetic/simulated light
  curves for injected-signal testing and evaluation [Q11]. The user asked
  that the specific real data source be identified during downstream design
  work rather than fixed now [Q11] [assumption].

## Sources

See `intent-capture-questions.md` for the full source register and answer
provenance ([desc], [scope], [Q1]-[Q12]).

## Assumptions & Open Questions

- The exact public archival data source(s) and access method for real light
  curves are not yet selected; the user asked that this be identified
  during a later design stage rather than fixed now [Q11] [assumption].
