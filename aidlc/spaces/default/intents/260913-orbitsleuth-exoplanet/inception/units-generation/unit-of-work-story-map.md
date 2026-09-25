# Unit ↔ Story Map — OrbitSleuth

Each story has one **implementing unit**, which owns its acceptance and is
the traceability target. Stories that also need work in other units list
those units under **Also touches**.

## Story Assignment

| Story | Title (short) | Implementing Unit | Directory | Also touches |
|-------|---------------|-------------------|-----------|--------------|
| US0.1 | Repo and quality checks | U1 | u1-repo-foundation | — |
| US0.2 | Data-source spike | U8 | u8-curation-tool | U9 (archive choice) |
| US0.3 | Shared API contract | U2 | u2-api-contract | U5, U6 |
| US0.4 | Engine core | U3 | u3-analysis-core | — |
| US0.5 | Vetting checks | U3 | u3-analysis-core | — |
| US0.6 | Score, rank, validate | U3 | u3-analysis-core | — |
| US0.7 | Serve catalog and curves | U5 | u5-backend-service | — |
| US1.1 | Browse curated curves | U6 | u6-web-explore | U5 |
| US1.2 | Filter real/synthetic | U6 | u6-web-explore | — |
| US1.3 | Curve provenance | U6 | u6-web-explore | — |
| US1.4 | Featured example | U6 | u6-web-explore | U7 |
| US2.1 | Zoom and pan chart | U6 | u6-web-explore | — |
| US2.2 | Raw/detrended toggle | U6 | u6-web-explore | U3 (detrended series) |
| US2.3 | Folded view | U6 | u6-web-explore | U5 (period in result) |
| US3.1 | Fast pre-computed result | U7 | u7-web-investigate | U5 |
| US3.2 | Live step-by-step trace | U7 | u7-web-investigate | U5 |
| US3.3 | Partial result | U7 | u7-web-investigate | U3 |
| US3.4 | Failure vs low confidence | U5 | u5-backend-service | U7 |
| US3.5 | Paused live runs | U7 | u7-web-investigate | U4, U5 |
| US4.1 | Ranked hypotheses | U7 | u7-web-investigate | U3 |
| US4.2 | Inspect evidence | U7 | u7-web-investigate | U3 |
| US4.3 | Plain-language explanation | U4 | u4-guarded-services | U5, U7 |
| US4.4 | Advisory wording | U4 | u4-guarded-services | U6, U7 (static strings) |
| US4.5 | Eclipsing binary vs transit | U3 | u3-analysis-core | — |
| US5.1 | Accept/reject/change | U7 | u7-web-investigate | — |
| US5.2 | Annotate | U7 | u7-web-investigate | — |
| US5.3 | Re-run with parameters | U7 | u7-web-investigate | U5 |
| US5.4 | Request more evidence | U7 | u7-web-investigate | U3, U5 |
| US5.5 | Keep decisions after reload | U7 | u7-web-investigate | — |
| US6.1 | Catalog + synthetic generator | U8 | u8-curation-tool | — |
| US6.2 | Fetch real curves | U8 | u8-curation-tool | — |
| US6.3 | Pre-compute results | U8 | u8-curation-tool | U5 |
| US6.4 | Frozen KOI sample + mapping | U9 | u9-evaluation-tool | — |
| US6.5 | Run evaluation + publish | U9 | u9-evaluation-tool | U5 (serve report), U6 (About view) |
| US6.6 | Cost and rate limits | U4 | u4-guarded-services | U5 |
| US6.7 | Trace and reproduce runs | U5 | u5-backend-service | — |

## Cross-Cutting Stories

- **US0.3 (contract)**: every unit that crosses the wire implements its side.
  Drift is caught by the U2 contract test.
- **US4.4 (advisory wording)**: the runtime check sits in U4. The static-string
  scan covers the U6 and U7 copy.
- **US3.4 (failure vs low confidence)**: the structured failure is produced in
  U5 and rendered in U7.
- **US6.5 (evaluation)**: the report is produced by U9, served by U5, and
  displayed in U6's About & accuracy view.
- **E2E smoke tests**: smoke 1 spans U6 → U7 with U5. It is owned by U7
  (see stories.md).

## Story Order Within Each Unit

Dependencies between stories (stories.md) set this order:

- **U1**: US0.1
- **U2**: US0.3
- **U3**: US0.4, then US0.5, then US0.6, then US4.5
- **U4**: US6.6, then US4.3, then US4.4
- **U5**: US0.7, then US6.7, then US3.4
- **U6**: US1.1, then US1.2, US1.3, US2.1, then US2.2, US2.3, then US1.4
- **U7**: US3.1, then US3.2, US3.3, US3.5, US4.1, then US4.2, then US5.1,
  then US5.2, US5.3, US5.4, US5.5
- **U8**: US0.2, then US6.1, then US6.2, then US6.3
- **U9**: US6.4, then US6.5

## Coverage Verification

- **Stories assigned**: 36 of 36. Every story has exactly one implementing
  unit.
- **Units with stories**:
  - U1: 1
  - U2: 1
  - U3: 4
  - U4: 3
  - U5: 3
  - U6: 7
  - U7: 11
  - U8: 4
  - U9: 2
- **Result**: every unit has at least one story, and no story is orphaned.

## Sources

- `inception/user-stories/stories.md`
- `inception/units-generation/unit-of-work.md`
- `inception/domain-design/traceability.json` (story → component mapping)

## Assumptions & Open Questions

- [assumption] US1.4 depends on US3.1 (in U7). U6's featured-example CTA
  therefore lands after U7's pre-computed result path exists. This is a
  story-level dependency for Delivery Planning, not a unit edge.
