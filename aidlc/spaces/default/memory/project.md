# Project-Level Rules

> Project-specific specialisation and corrections. Loaded after `org.md` and
> `team.md` as strict-additive guidance; contradictions with broader policy
> are rejected. Populated by practices-discovery and the self-learning loop.
>
> Use sparingly: most teams don't need a project layer. Reach for it
> only when this specific project needs stable, durable guidance beyond the
> team practice (for example, package-specific release checks or an additional
> regression suite for a legacy component).

## Way of Working

<!-- Project-specific specialisation. Example: -->
<!-- This monorepo requires package-scoped branch names and a package owner -->
<!-- review in addition to the team's normal merge policy. -->

## Walking Skeleton

<!-- Project-specific specialisation. Example: -->
<!-- The walking skeleton must exercise the legacy service adapter as well -->
<!-- as the new service boundary. -->

## Testing Posture

<!-- Project-specific specialisation. -->

## Change Control

<!-- Project-specific. Mode: strict or relaxed. Strict here holds for every intent and cannot be changed from chat. -->

## Deployment

<!-- Project-specific specialisation. -->

## Code Style

<!-- Project-specific specialisation. -->

## Tech Stack

<!-- Technology choices locked for this project. -->

## Decided

<!-- Decisions made in earlier stages that should not be re-asked. -->
<!-- Format: DECIDED: [decision] (Stage [slug], [date]) -->

## Scope Overrides

<!-- Custom scope rules for this project. -->

## Forbidden

<!-- Populated by practices-discovery affirmation gate. -->
<!-- Format: NEVER [behavior] (affirmed [date]) -->
<!-- Example: NEVER throw exceptions across service layer boundaries (affirmed 2026-05-17) -->

- NEVER provision cloud resources or begin a deployment build-out before a separate Unit-of-Work approval is given (confirmed hard constraint, `intent-statement.md`, `constraint-register.md` BC-2). (affirmed 2026-09-25)

- NEVER let the AI unilaterally declare a "discovery" — its role is advisory only (confirmed hard constraint, `intent-statement.md`). (affirmed 2026-09-25)

## Mandated

<!-- Populated by practices-discovery affirmation gate. -->
<!-- Format: ALWAYS [behavior] (affirmed [date]) -->
<!-- Example: ALWAYS use Result<T,E> for fallible operations in service layer (affirmed 2026-05-17) -->

- ALWAYS require every AI-generated hypothesis to cite the specific evidence supporting it and to state a confidence/uncertainty level, never a bare verdict (confirmed hard constraint, `intent-statement.md` TC-5). (affirmed 2026-09-25)

- ALWAYS require explicit human accept/reject/annotate action on an AI-generated hypothesis before it is treated as final, and allow the human to intervene at any point in the investigation (confirmed hard constraint, `intent-statement.md` TC-6). (affirmed 2026-09-25)

- ALWAYS back AI analysis with real, tool-invoked signal-processing/astronomy analysis (e.g. period-detection, detrending, vetting statistics) rather than free-form LLM reasoning (confirmed hard constraint, `intent-statement.md` TC-4). (affirmed 2026-09-25)

- ALWAYS run automated secret scanning — GitHub secret scanning or a pre-commit gitleaks hook — before any commit reaches the repository (confirmed hard constraint, interview Q11/Q12; escalated from a Code Style suggestion to Mandated on the devsecops agent's explicit recommendation, since a leaked credential is a real, hard-to-reverse consequence and there is no second reviewer to catch it). (affirmed 2026-09-25)

## Corrections

<!-- Project-specific corrections from human feedback. -->
<!-- Format: NEVER/ALWAYS [behavior] (learned [date]) -->
- Treat OrbitSleuth as a solo-founder resume/portfolio initiative: one decision-maker (the author) plus two influencer audiences (hiring managers/recruiters, general public visitors) -- not a multi-stakeholder org. (learned 2026-09-13) <!-- cid:260913-orbitsleuth-exoplanet:intent-capture:b711a11cff00454636ba33c89536292f65ff468e4fbab30b17538eefcfe5dafd -->
- `aidlc engine review-brief summary` is currently broken ("does not export main(argv)"); rely on the aidlc-log.ts decision/answer receipts to satisfy the summary-confirmation checkpoint instead of the missing brief. (learned 2026-09-13) <!-- cid:260913-orbitsleuth-exoplanet:intent-capture:ca3c55c87bafac3fecdfa07843201cc83f01ef3c62923a234dbca8a52c4a212c -->
- The real light-curve data source is intentionally left as an open assumption at intent capture; identify and select it during a later design stage rather than fixing it now. (learned 2026-09-13) <!-- cid:260913-orbitsleuth-exoplanet:intent-capture:5bdcb224f052b260cf94a457e913e842dd7e89a8bffba5faae8a7728bdd7aeb1 -->
- Before naming a specific external dataset or data source in a feasibility or design artifact, verify via web search that it is real, current, and accessible rather than relying on training knowledge alone. (learned 2026-09-13) <!-- cid:260913-orbitsleuth-exoplanet:feasibility:20d61b6cf0c9dcf0a313b6ae8b5979d1e7aee5ae97c9adc65706d71ecbe862cc -->
- When a sequencing heuristic (e.g. value-first) and a specific ordering dependency are both confirmed by the user, keep the heuristic as the overall approach and carry the dependency forward as an explicit hard constraint rather than treating the two as contradictory. (learned 2026-09-13) <!-- cid:260913-orbitsleuth-exoplanet:scope-definition:3f6ba71ba81c8aa735f0d5d53668251a67f05fcb639ecaedb285dcbcbc60cafe -->
- The product-lead reviewer agent has occasionally opened its review file with `# Review` (H1) instead of the required `## Review` (H2), which the completion logger rejects as an unowned top-level heading. If this happens, use the sanctioned retry-pending recovery (re-dispatch the reviewer to fix only the heading level) rather than treating it as a genuine NOT-READY. (learned 2026-09-13) <!-- cid:260913-orbitsleuth-exoplanet:rough-mockups:6b139fdc57ece18b596ffda40bf77418bd15818bc210a5d32ba949fde3fed484 -->
