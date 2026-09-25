# NFR Requirements — u1-repo-foundation — Questions

## Sources

- [unit] `inception/units-generation/unit-of-work.md` (U1: repository skeleton, lint and format, coverage, CI, audit, gitleaks, lockfiles)
- [stories] `inception/user-stories/stories.md` (US0.1: AC0.1.1 to AC0.1.4)
- [practices] `inception/practices-discovery/team-practices.md` (Black, Ruff with `S` rules, Prettier, ESLint, pytest-cov, Vitest, GitHub Actions, Dependabot or pip-audit/npm audit, committed lockfiles)
- [project Mandated] secret scanning before any commit reaches the repository

Already settled and not re-asked:

- The tools listed above.
- The 80% coverage floor.
- CI with no deploy step.
- npm with a committed `package-lock.json` for the frontend.

U1 sets up the repository that every other unit builds in, so the tooling
versions it pins apply project-wide.

## Q1. Which Python version and dependency manager?

The analysis engine depends on scientific libraries (for example numpy and
astropy), and they must support the chosen Python version.

- A. Python 3.13, managed with uv (fast; a single `uv.lock` lockfile)
- B. Python 3.12 with Poetry (`poetry.lock`)
- C. Python 3.13 with pip-tools (`requirements.txt` with pinned hashes)
- X. Other (please specify)

[Answer]: A (2026-09-25T15:11:48Z, **Mode:** chat — user accepted the recommended option: "All good")

## Q2. Which frontend framework and build tool?

U1's lint and test config (ESLint, Vitest, TypeScript `strict`) must match
the framework the frontend units (U6, U7) will use.

- A. React with TypeScript on Vite: the widest ecosystem of headless accessible primitives and charting libraries, and Vitest is native to it
- B. Svelte (SvelteKit) with TypeScript: smaller bundles, fewer headless-primitive options
- C. Vue 3 with TypeScript on Vite
- X. Other (please specify)

[Answer]: A (2026-09-25T15:11:48Z, **Mode:** chat — user accepted the recommended option: "All good")

## Q3. How do the local pre-commit checks run?

Secret scanning must run before any commit reaches the repository (a
mandated rule).

- A. The `pre-commit` framework with gitleaks, Ruff, Black, Prettier, and ESLint hooks; CI also runs gitleaks as a backstop, and GitHub secret scanning is enabled on the repository
- B. gitleaks only as a pre-commit hook; everything else runs in CI
- X. Other (please specify)

[Answer]: A (2026-09-25T15:11:48Z, **Mode:** chat — user accepted the recommended option: "All good")

## Q4. When should a vulnerable dependency fail the build?

- A. Fail CI on any High or Critical vulnerability; report Medium and Low in the CI log; Dependabot opens weekly update pull requests
- B. Fail CI on any known vulnerability of any severity
- C. Report only; never fail the build
- X. Other (please specify)

[Answer]: A (2026-09-25T15:11:48Z, **Mode:** chat — user accepted the recommended option: "All good")

## Q5. How long may the pre-merge CI run take?

A time limit keeps the solo feedback loop fast.

- A. At most 10 minutes for lint, type-check, tests, and coverage on the fixture-only suite; performance and evaluation runs stay outside this gate
- B. No limit
- X. Other (please specify)

[Answer]: A (2026-09-25T15:11:48Z, **Mode:** chat — user accepted the recommended option: "All good")

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Python 3.13, managed with uv and a single `uv.lock` (Q1: A)
- Frontend: React with TypeScript on Vite; Vitest for tests (Q2: A)
- `pre-commit` framework with gitleaks, Ruff, Black, Prettier, ESLint hooks; gitleaks also in CI; GitHub secret scanning enabled (Q3: A)
- CI fails on High or Critical dependency vulnerabilities, reports Medium and Low; Dependabot weekly (Q4: A)
- Pre-merge CI finishes in 10 minutes or less; performance and evaluation runs stay outside it (Q5: A)

Does this all look correct before I generate the NFR requirements for u1-repo-foundation?

- Looks correct
- Request changes

[Answer]: Looks correct
