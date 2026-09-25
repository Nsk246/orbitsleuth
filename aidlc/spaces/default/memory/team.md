# Team-Level Rules

> This team's affirmed practices and corrections. Loaded after `org.md` as
> strict-additive guidance; contradictions with broader policy are rejected.
> Populated by the practices-discovery affirmation gate. Edit at the gate,
> not directly.

## Way of Working

We are a solo builder with no team to coordinate branch reviews against, so
the org default of trunk-based development applies in its simplest form:
one person committing short-lived feature branches straight to `main`, no
pull-request approval gate needed since there is only one contributor
(confirmed, Q1). Branches stay short-lived (resolved within the same
session or day) to avoid merge debt piling up against a stale branch. For
Construction worktrees, the base branch is `main` and the merge target is
`main`, squash-merged per Bolt so `main` stays a clean, linear history that
maps 1:1 to the delivery-planning Bolt sequence — useful here specifically
because this project is also a portfolio artifact a reviewer may read
commit-by-commit.

## Walking Skeleton

Skipped (confirmed, Q2). We go straight to the first real feature/Bolt
rather than building a separate thin end-to-end slice first. The first Bolt
runs like any other Bolt — there is nothing to bootstrap ahead of it.

## Testing Posture

- **Methodology**: custom
- **Ordering**: for the analysis/vetting layer, write the labeled-data
  fixture test first and implement the detection logic to satisfy it
  (fixtures-first); for the API/service boundary and the frontend,
  implement each layer first and then write and run its automated tests
  (test-after) — confirmed, Q4.
- Per the `mvp` scope default, an 80% line-coverage floor applies to both
  the Python and JS/TS codebases.
- Coverage tooling (confirmed, Q3): Python uses `pytest` + `pytest-cov`
  (coverage.py backend), floor enforced via `--cov-fail-under=80`; JS/TS
  uses Vitest (or Jest) with its built-in coverage reporter, floor enforced
  via the runner's coverage-threshold config.
- CI (confirmed, Q3): a free GitHub Actions workflow runs lint + tests +
  coverage on every push, with no deployment step — this gives the
  portfolio a visible, verifiable quality signal without provisioning any
  cloud resource, so it does not conflict with the confirmed deferral of
  deployment work.
- Test-data strategy (confirmed, Q4): pre-merge tests (unit, integration,
  and accuracy-fixture tests) run only against committed synthetic/
  injected-signal fixtures and a small frozen sample of labeled KOI-table
  rows — never live NASA archive fetches. Live-archive access is reserved
  for the confirmed evaluation methodology as a separate, explicitly
  labeled validation run outside the pre-merge suite, keeping pre-merge CI
  fast and deterministic.
- Hard-constraint test obligations (confirmed, Q6): each of the three
  confirmed hard constraints gets its own specific automated test rather
  than documentation alone —
  - TC-4 (tool-backed analysis, never free-form reasoning): a provenance
    assertion in the analysis-layer tests confirming hypothesis evidence
    traces back to actual tool output objects (period-detection,
    detrending, vetting-statistics results), alongside the accuracy-fixture
    tests.
  - TC-5 (evidence + confidence, never a bare verdict): a contract/schema
    test on the analysis engine's output asserting an evidence list and a
    confidence score are always present and never null/bare.
  - TC-6 (human accept/reject/annotate before final): an integration test
    asserting the system cannot mark a hypothesis "final" without a human
    action recorded, spanning the review-workflow boundary.
- End-to-end scope (confirmed, Q5): one or two full-flow smoke tests
  covering the confirmed value stream (select light curve → run analysis →
  see ranked hypothesis with evidence → accept/reject/annotate); everything
  else is covered by unit tests (analysis functions, component logic) and
  the contract/integration tests above at the analysis↔frontend boundary
  and the human-review-gate invariant. This keeps the pyramid unit-heavy
  without leaving the integration seams untested.
- As a solo builder, "CI execution before merge" means the author runs (or
  the GitHub Actions workflow runs) the full check before every merge to
  `main`, not a multi-reviewer gate.

## Change Control

<!-- Affirmed by the team. Mode: strict or relaxed. Strict here holds for every intent and cannot be changed from chat. -->

## Deployment

No deployment activity is in scope yet — application code, cloud
provisioning, and any deployment plan are explicitly deferred pending a
separate Unit-of-Work approval, per the confirmed initial scope. When that
later phase begins, the confirmed starting point (Q7) is: deploy on merge
to a staging-like setup, with the same person personally deciding when to
promote anything to a production-like environment — the "manual approval"
step collapses to that one conscious decision rather than a second
person's sign-off. DAST (dynamic security testing) consideration is
explicitly deferred to that later deployment/infrastructure stage rather
than decided now, since no live endpoint exists yet to scan. An existing
personal AWS account is available for that later phase; cost-minimization
(free-tier-oriented services) is a confirmed hard constraint for whatever
gets provisioned then.

## Code Style

We follow the org default of deferring to project-level tool configuration
rather than inventing project-wide rules, specialized with the boundary,
error-handling, organization, and security conventions confirmed at
interview:

- Python (analysis backend): Black for formatting, Ruff for linting,
  configured in `pyproject.toml`. Ruff's built-in security-lint ruleset
  (bandit-equivalent, rule codes prefixed `S`) is enabled in the same
  config at no extra tooling cost (confirmed, Q11).
- JS/TS (interactive frontend): Prettier for formatting, ESLint for
  linting, configured at the repo root.
- Naming conventions follow language idioms (snake_case in Python,
  camelCase in JS/TS) with no project-wide rename rules unless later
  affirmed.
- API boundary and shared vocabulary (confirmed, Q8): API responses always
  use camelCase field names, translated from Python's internal snake_case
  at the serialization boundary. Hypothesis/evidence vocabulary
  (`transit` / `stellar_activity` / `noise`, `confidence`, `evidence`) is a
  single shared vocabulary, not independently named on each side.
- Layer boundaries (confirmed, Q8): the analysis engine is a plain,
  framework-independent Python package with no web-framework imports
  inside it, so it stays independently testable and reusable. The
  API/service layer is the only place that translates between the analysis
  engine's internal representation and the wire format. The frontend never
  re-implements analysis or business logic (e.g. re-deriving a confidence
  score client-side) — it only renders what the API returns.
- Error handling (confirmed, Q9): analysis-engine failures raise typed/
  domain exceptions rather than returning ambiguous nulls; the API layer
  catches these and returns a structured error response, distinct from a
  valid low-confidence hypothesis (which is a real result, not an error);
  no exception is silently swallowed anywhere in the ingestion → analysis →
  API chain, so a human reviewer always knows when something failed versus
  when analysis simply found nothing conclusive.
- File organization (confirmed, Q10): a single repository with a clear
  top-level split between the backend/analysis code and the frontend code.
  Exact directory naming is left to a later design stage.
- Type safety (confirmed, Q10): Python type hints on public analysis-engine
  functions; TypeScript `strict` mode on the frontend.
- Dependency and supply-chain hygiene (confirmed, Q11): free
  dependency-vulnerability alerts (GitHub Dependabot and/or `pip-audit` /
  `npm audit`) run in the same pre-merge check as lint and tests; lockfiles
  (e.g. `requirements.txt` with pinned versions or `poetry.lock`/`uv.lock`,
  and `package-lock.json`) are committed for reproducible builds.
- Lint/format and security checks run before merge; failures block the
  merge, same as the org default, adapted to a solo-builder pre-merge check
  (locally and/or via the GitHub Actions workflow) rather than a
  PR-blocking gate reviewed by others.
## Forbidden

<!-- Team-specific forbidden patterns -->

## Mandated

<!-- Team-specific mandates -->

## Corrections

<!-- Self-learning loop appends here. -->
