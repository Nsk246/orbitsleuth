# Team Allocation — OrbitSleuth

A **Bolt** is one build pass over one or more units of work that ends in
something runnable. A **mob** is the group that builds a Bolt together.

Team Formation was skipped for this `mvp` workflow. OrbitSleuth has one
builder [project Corrections] [Q6], so there is a single mob:

- **Builder**: the AI developer (aidlc-developer-agent) writes all code.
- **Approver**: the author reviews and approves each design and build step.

| Bolt | Units | Mob | Author approval points |
|------|-------|-----|------------------------|
| B1 Foundation and contract | U1, U2 (+ spike US0.2) | aidlc-developer-agent | Each unit's design and code gates; the spike's archive choice |
| B2 Web explore | U6 | aidlc-developer-agent | Design and code gates |
| B3 Analysis core | U3 | aidlc-developer-agent | Design and code gates; review of the small-sample validation result |
| B4 Guarded services and backend | U4, U5 | aidlc-developer-agent | Design and code gates; LLM provider choice (NFR Design) |
| B5 Web investigate | U7 | aidlc-developer-agent | Design and code gates |
| B6 Curation | U8 | aidlc-developer-agent | Design and code gates; final catalog selection and the featured example |
| B7 Evaluation | U9 | aidlc-developer-agent | Design and code gates; the published baseline |

There is one team, so no cross-team coordination board (Program Board) is
needed. Construction runs in this session, one unit at a time [Q5].

## Sources

- [project Corrections] `aidlc/spaces/default/memory/project.md`: solo-founder initiative
- `inception/delivery-planning/bolt-plan.md`
- [Q5], [Q6] `inception/delivery-planning/delivery-planning-questions.md`

## Assumptions & Open Questions

None.
