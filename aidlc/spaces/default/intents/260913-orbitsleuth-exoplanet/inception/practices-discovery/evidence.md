# Evidence — OrbitSleuth

## Project Type

Greenfield. `aidlc-state.md` and the ideation-phase artifacts confirm no
existing code, no git history, and no prior team conventions to
reverse-engineer. Consequently, no brownfield inputs (code-structure,
technology-stack, dependencies, code-quality-assessment, architecture,
business-overview) were available or expected; their absence is not a
coverage gap for this stage.

## Participants and What Each Inspected

1. **Lead (aidlc-pipeline-deploy-agent), draft pass.** Read the five
   `org.md` sections (Way of Working, Walking Skeleton, Testing Posture,
   Deployment, Code Style) as suggested defaults, plus
   `ideation/intent-capture/intent-statement.md` (hard product-behavior
   constraints: evidence-linked hypotheses, human-in-control review,
   tool-backed analysis; solo-builder/no-team framing),
   `ideation/scope-definition/scope-document.md` (`mvp` scope boundary,
   production-grade AWS deployment out of scope, value-first sequencing),
   `ideation/feasibility/feasibility-assessment.md` (Python analysis /
   JS-TS frontend / AWS-later technology shape, solo-builder skill
   profile), and `ideation/feasibility/constraint-register.md` (TC-2, TC-3,
   BC-1, BC-2, OC-1, OC-3). These seeded every section of the draft.

2. **aidlc-quality-agent (blind support review).** Assessed the draft's
   Testing Posture against the confirmed hard constraints and the project's
   architecture. Identified that coverage tooling was unnamed, that
   "CI execution before merge" had no concrete location, that TC-4/TC-5/
   TC-6 had no test-pattern obligations, that the accuracy-fixture note
   lacked an explicit fixture/network split, that a mixed (`custom`)
   methodology was worth surfacing rather than uniform test-after, and that
   e2e scope and frontend test tooling were unstated.

3. **aidlc-developer-agent (blind support review).** Assessed naming,
   layer boundaries, error handling, file organization, and type safety.
   Identified the missing API-boundary casing/vocabulary convention (tied
   to the Capability 3→5 hard dependency in `intent-backlog.md`), the need
   to state layer boundaries explicitly in Code Style rather than only
   implying them from test ordering, the complete absence of an
   error-handling convention despite the Construction-phase guardrails,
   the need to at least confirm a top-level backend/frontend split, and the
   absence of any type-hint/strictness stance.

4. **aidlc-devsecops-agent (blind support review).** Assessed lint/format,
   SAST/DAST, secret scanning, dependency scanning, and supply-chain
   controls against the confirmed no-regulatory-requirement context
   (`constraint-register.md` RC-1) and cost-minimization constraint (BC-1).
   Confirmed the draft's lint/format choices were sound; recommended
   enabling Ruff's free built-in security-lint ruleset; flagged SAST/DAST
   as correctly out of scope for now (SAST optionally covered by CodeQL
   later, DAST deferred to the future deployment stage); and identified
   secret scanning as the most significant gap, explicitly recommending it
   be promoted to `## Mandated` rather than left as a Code Style
   suggestion, plus dependency-vulnerability alerts and lockfile hygiene.

5. **Human interview (12 questions, `practices-discovery-questions.md`).**
   Each blind-review gap and each draft suggestion was posed as a plain-
   language question; every answer was logged and the consolidated summary
   was confirmed "Looks correct." Decisions reached:
   - Q1: Solo trunk-based development, no PR-review gate.
   - Q2: Skip the walking skeleton; go straight to the first real
     feature/Bolt.
   - Q3: `pytest`/`pytest-cov` and Vitest/Jest with an 80% coverage floor,
     plus a free GitHub Actions workflow (lint + test + coverage, no
     deploy step).
   - Q4: Pre-merge tests use only committed synthetic/fixture data and a
     frozen labeled sample (never live NASA archive fetches);
     fixtures-first specifically for the analysis/vetting layer, test-after
     for the API and frontend — recorded as `Methodology: custom`.
   - Q5: One or two full-flow e2e smoke tests covering the confirmed value
     stream; everything else via unit/component tests.
   - Q6: Each of TC-4, TC-5, and TC-6 gets its own specific automated test.
   - Q7: For the later Unit-of-Work deployment phase, deploy-on-merge to a
     staging-like setup with personal promotion decisions; DAST deferred to
     that stage.
   - Q8: Shared camelCase API vocabulary translated from Python
     snake_case; analysis engine stays plain/self-contained with the API
     as the sole translation layer.
   - Q9: Explicit typed/domain exceptions in the analysis engine, a
     structured API error response distinct from a valid low-confidence
     result, no silent failure anywhere in the chain.
   - Q10: Single repo with a top-level backend/frontend split, Python type
     hints on public analysis functions, TypeScript `strict` mode.
   - Q11: Ruff security-lint ruleset, automated secret scanning before any
     commit, free dependency-vulnerability alerts, committed lockfiles —
     all confirmed.
   - Q12: No additional hard constraints beyond the five already
     confirmed, except that the Q11 secret-scanning practice is itself
     promoted to `## Mandated` per the devsecops agent's recommendation.

## Resolution of Prior Unresolved Uncertainty

The draft's "Unresolved Uncertainty" section listed four open items. All
four are now resolved by the interview, with no remaining uncertainty:

- Walking-skeleton stance → resolved (Q2: skip).
- Test-after vs. a different cadence for the solo builder → resolved (Q4:
  `custom` methodology, fixtures-first for analysis, test-after elsewhere).
- Deployment cadence/topology for the later Unit-of-Work phase → resolved
  as a recorded future default (Q7), with DAST explicitly deferred rather
  than decided now.
- Additional hard constraints beyond the three already confirmed → resolved
  (Q12: no additional product-behavior constraints; one new
  process/security constraint — secret scanning — added to `## Mandated`).

No further unresolved uncertainty remains from this stage's review and
interview process.
