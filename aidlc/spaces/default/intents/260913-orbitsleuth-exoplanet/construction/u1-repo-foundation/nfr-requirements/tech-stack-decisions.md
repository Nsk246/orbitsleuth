# Tech Stack Decisions — u1-repo-foundation

U1 pins the project-wide toolchain. Later units inherit these choices unless
a later ADR supersedes them.

## Decisions

| Area | Choice | Rationale | Alternatives rejected |
|------|--------|-----------|-----------------------|
| Python version | 3.13 | Current stable line supported by the scientific stack the engine needs (numpy, astropy); verify library support when the lockfile is first resolved in Code Generation | 3.12 (older); 3.14 (library support may lag) [Q1] |
| Python dependency manager | uv, with a committed `uv.lock` | Fast installs keep CI under 10 minutes; one lockfile; `uv sync --locked` gives reproducible builds | Poetry (slower resolver); pip-tools (multiple files) [Q1] |
| Python quality tools | Black (format), Ruff with `S` rules (lint and security), pytest + pytest-cov (80% floor), mypy on public analysis functions | Team practice; Ruff `S` covers bandit-equivalent checks at no extra tool cost | Bandit as a separate tool (duplicate of Ruff `S`) [practices] |
| Frontend framework and build | React + TypeScript (`strict`) on Vite | Widest choice of headless accessible primitives and charting libraries, which the refined mockups require; Vitest is native to Vite | Svelte (fewer headless options); Vue (smaller primitive ecosystem) [Q2] |
| JS package manager | npm, with a committed `package-lock.json`; CI uses `npm ci` | Team practice; zero extra tooling | pnpm, yarn [practices] |
| JS quality tools | Prettier, ESLint (TypeScript + security plugin set), Vitest with v8 coverage (80% threshold), `tsc --noEmit` | Team practice | Jest (slower with Vite) [practices] |
| Local hooks | `pre-commit` framework running gitleaks, Ruff, Black, Prettier, ESLint | One hook config for both languages; enforces the mandated secret scan before commits [project Mandated] | Husky (JS-only) [Q3] |
| CI | GitHub Actions, no deploy step; jobs: lint and format, type-check, Python tests + coverage, TS tests + coverage, `pip-audit`, `npm audit`, gitleaks | Free for public repositories; visible quality signal for the portfolio [practices] | Self-hosted CI (cost, upkeep) |
| Dependency updates | Dependabot weekly for pip/uv, npm, and GitHub Actions | Free, native to GitHub [Q4] | Renovate (more config) |
| Repository layout | `backend/` (Python: analysis core, guarded services, backend service, offline tools as separate packages), `frontend/` (React app), `contract/` (U2 schemas and generated types), `data/` (versioned data files, path confirmed in Functional Design of U8/U9) | Top-level backend/frontend split confirmed in practices; contract kept separate so both sides depend on it | Separate repositories (more overhead for a solo builder) [practices] |

## Version Pinning Policy

- Tool versions are pinned in `pyproject.toml`, `package.json`, and
  `.pre-commit-config.yaml`. GitHub Actions are pinned by commit SHA
  (NFR7.6).
- Runtime language versions are pinned in `.python-version` and in the
  `engines` field of `package.json`. The Node LTS version is chosen at Code
  Generation and verified to be a current LTS.

## Sources

- [practices] `inception/practices-discovery/team-practices.md`
- [project Mandated] `aidlc/spaces/default/memory/project.md`
- [refined-mockups] `inception/refined-mockups/design-system-mapping.md` (headless primitives)
- [Q1]–[Q5] `construction/u1-repo-foundation/nfr-requirements/nfr-requirements-questions.md`

## Assumptions & Open Questions

- [assumption] numpy and astropy publish wheels for Python 3.13; confirm this when the first `uv lock` runs, and fall back to 3.12 if they do not.
- Open: the exact Node LTS version is chosen and verified at Code Generation.
