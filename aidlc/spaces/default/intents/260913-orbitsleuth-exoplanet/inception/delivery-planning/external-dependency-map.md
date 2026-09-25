# External Dependency Map — OrbitSleuth

A **Bolt** is one build pass over one or more units of work that ends in
something runnable. This map lists what comes from outside the project and
which Bolt needs it. Nothing here requires cloud provisioning. That stays
forbidden until a separate deployment decision [project Forbidden].

| Dependency | Owner | Lead time | Needed by | Fallback if it slips [Q3] |
|------------|-------|-----------|-----------|---------------------------|
| Public light-curve archive (chosen and web-verified in spike US0.2) | External archive operator; the author runs access | ~1 day spike (B1) | B3 validation run, B6 real catalog, B7 sample curves | Keep building on synthetic curves and committed fixtures; move the B3 validation run to B6 |
| NASA Exoplanet Archive KOI table (labels for evaluation) | NASA Exoplanet Archive | Same day (public, fetched once, then versioned) | B3 validation run, B7 | Use the small frozen label sample already planned for fixtures |
| LLM provider account and API key (explanation) | Author (account sign-up and billing) | Hours to days | B4 (optional path) | Ship with the explanation marked "unavailable"; nothing else blocks |
| GitHub repository with Actions and secret scanning | Author | Minutes | B1 | Run the same checks locally as the pre-merge gate |

No other teams, approvals, or partner APIs are involved. OrbitSleuth is
otherwise fully contained.

## Sources

- `inception/contract-design/contract-summary.md` (C6, C7 external boundaries)
- `ideation/feasibility/feasibility-assessment.md` (KOI table accessibility)
- [project Forbidden] `aidlc/spaces/default/memory/project.md`
- [Q3] `inception/delivery-planning/delivery-planning-questions.md`

## Assumptions & Open Questions

- [assumption] The GitHub repository already exists or can be created by the author at no cost.
