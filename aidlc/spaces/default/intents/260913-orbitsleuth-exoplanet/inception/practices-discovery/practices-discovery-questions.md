# Practices Discovery — Interview Questions

## Sources

- [draft] Lead draft: `team-practices.md`, `discovered-rules.md`, `evidence.md` (built from `org.md` defaults + the ideation-phase artifacts, greenfield).
- [contrib:quality] `contributions/aidlc-quality-agent.md` — testing tooling, CI, test-data, methodology, e2e scope gaps.
- [contrib:developer] `contributions/aidlc-developer-agent.md` — API-boundary naming, layer boundaries, error handling, file organization, type-safety gaps.
- [contrib:devsecops] `contributions/aidlc-devsecops-agent.md` — SAST, secret scanning, dependency scanning, supply-chain gaps.

## Q1. You're building solo, committing straight to `main` with short-lived branches and no second person to review pull requests before merge. Does that match how you want to work?

- A. Yes, that's right — solo trunk-based, no PR-review gate needed
- B. No — I do want a review-like step even working alone (e.g. a personal checklist before merging)
- C. Not yet defined
- [Answer]: A

## Q2. Build a thin end-to-end slice first? A walking skeleton is a minimal version that runs the whole way through, built first to prove the pieces connect before the real features go in.

- A. Yes — build one thin slice first (one light curve → one tool-backed analysis call → one rendered hypothesis with evidence) before adding breadth, to de-risk the frontend/backend integration early
- B. No — skip the ceremony and start directly on the first real feature/Bolt
- C. Not sure — you decide based on what best serves a fast, demoable first result
- [Answer]: B

## Q3. For testing tools and where tests run: pytest + pytest-cov for the Python analysis backend, and Vitest (or Jest) for the JS/TS frontend, both enforcing the 80% coverage floor — plus a free GitHub Actions workflow that runs lint + tests + coverage on every push (no deployment step, so it doesn't conflict with deferring cloud provisioning). Does that work for you?

- A. Yes to both — those testing tools, and the free GitHub Actions workflow for visible CI history
- B. Yes to the testing tools, but skip GitHub Actions — just a local pre-merge check is enough for now
- C. Different testing tools (please specify in Other)
- D. Not yet defined
- [Answer]: A

## Q4. For test data and methodology: should pre-merge tests run only against committed synthetic/fixture data and a small frozen sample of labeled data (never live NASA archive fetches, to keep tests fast and reliable), and should the analysis/vetting layer specifically use "write the labeled-data test first, then the detection logic" (fixtures-first) while the API and frontend stay test-after?

- A. Yes to both — fixture-only pre-merge tests, and fixtures-first specifically for the analysis/vetting layer
- B. Yes to the fixture-only pre-merge tests, but keep test-after uniformly across all layers (including analysis) — no fixtures-first
- C. Different approach (please specify in Other)
- D. Not yet defined
- [Answer]: A

## Q5. How much end-to-end (full browser flow) testing do you want? One or two full-flow smoke tests covering the whole confirmed value stream (select a light curve → run analysis → see the ranked hypothesis with evidence → accept/reject/annotate), with everything else covered by faster unit and component-level tests?

- A. Yes — one or two full-flow smoke tests, unit/component tests for everything else
- B. More end-to-end coverage than that
- C. Skip end-to-end tests for the MVP
- D. Not yet defined
- [Answer]: A

## Q6. Should each of your three "never compromise on this" product rules — every hypothesis must cite evidence and a confidence level, a human must explicitly accept/reject/annotate before anything counts as final, and analysis must be backed by real tools rather than free-form AI reasoning — get its own specific automated test that fails if the rule is ever violated, rather than being documentation-only?

- A. Yes — each of those three rules should have a specific automated test backing it
- B. No — documenting them is enough for now; tests can follow later
- C. Not yet defined
- [Answer]: A

## Q7. Deployment stays fully on hold until you separately approve the Unit of Work, as already confirmed. When that later phase does start, does "deploy on merge to a staging-like setup, with you personally deciding when to promote to anything production-like" sound right as the starting point?

- A. Yes, that's a reasonable starting point for later
- B. No — a different deployment cadence should be recorded now (please specify in Other)
- C. Leave it completely open for later — don't pre-record anything
- [Answer]: A

## Q8. For how the analysis backend and the frontend talk to each other: should API responses always use camelCase field names (translated from Python's internal snake_case) and a single shared vocabulary for hypothesis terms (`transit` / `stellar_activity` / `noise`, `confidence`, `evidence`) rather than each side inventing its own names? And should the analysis engine stay a plain, self-contained Python package (no web-framework code inside it) with the API layer as the only translation point, so the frontend never re-implements any analysis logic itself?

- A. Yes to both — shared camelCase API vocabulary, and analysis engine kept plain/self-contained with the API as the sole translation layer
- B. Different naming/boundary approach (please specify in Other)
- C. Not yet defined
- [Answer]: A

## Q9. For handling failures: should analysis failures (e.g. a tool couldn't converge) raise a clear internal error rather than returning something ambiguous, should the API send back a distinct "analysis failed" response instead of quietly returning nothing, and should no failure anywhere in the chain ever be silently swallowed — so you (the human reviewer) always know when something went wrong versus when the AI simply found nothing conclusive?

- A. Yes — explicit errors throughout, always distinguishable from a low-confidence (but real) result
- B. A lighter-weight approach is fine for now (please specify in Other)
- C. Not yet defined
- [Answer]: A

## Q10. For code organization and type safety: one repository with a clear top-level split between the backend/analysis code and the frontend code, Python type hints on the public analysis functions, and TypeScript `strict` mode on the frontend?

- A. Yes to all three
- B. Some but not all of these (please specify which in Other)
- C. Not yet defined
- [Answer]: A

## Q11. For security and dependency hygiene at effectively zero cost: enable Ruff's built-in security-lint rules for the Python code, add automated secret scanning before anything reaches the repository (GitHub's built-in scanning or a local pre-commit check), enable free dependency-vulnerability alerts (e.g. GitHub Dependabot, or `pip-audit`/`npm audit` in the same pre-merge check), and commit lockfiles so builds stay reproducible?

- A. Yes to all of these
- B. Only some of these (please specify which in Other)
- C. Not a priority right now
- D. Not yet defined
- [Answer]: A

## Q12. Are there any other hard "always" or "never" rules you want locked in now, beyond the three already confirmed (evidence-linked hypotheses, human accept/reject/annotate control, tool-backed analysis) and the two already confirmed prohibitions (no cloud provisioning before Unit-of-Work approval, AI never unilaterally declares a discovery)?

- A. No, those five already cover it
- B. Yes, add one or more (please specify in Other)
- [Answer]: A

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Looks correct
- Request changes

[Answer]: Looks correct
