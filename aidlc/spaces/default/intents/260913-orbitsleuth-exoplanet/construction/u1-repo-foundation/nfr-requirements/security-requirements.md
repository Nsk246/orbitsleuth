# Security Requirements — u1-repo-foundation

U1 is the repository skeleton and the pre-merge quality and security gate
(unit kind: packaging). It has no runtime, no endpoints, and no data. Its
attack surface is the supply chain: source control, dependencies, and CI.
These requirements refine inception NFR7 (security) and NFR10
(maintainability) [requirements].

## Threat Summary (STRIDE, supply-chain scope)

| Threat | Asset | Example | Control (requirement) |
|--------|-------|---------|------------------------|
| Information disclosure | Secrets (LLM API key, future cloud credentials) | A key committed to git and pushed to a public repository | NFR7.1, NFR7.2, NFR7.3 |
| Tampering | Dependencies | A malicious or vulnerable package version pulled in by an unpinned range | NFR7.4, NFR7.5 |
| Tampering | CI workflow | A workflow change grants write tokens or runs untrusted code | NFR7.6 |
| Elevation of privilege | CI token | An over-broad `GITHUB_TOKEN` permission used by a compromised step | NFR7.6 |
| Information disclosure | Source code | Insecure patterns (for example `eval`, shell injection, weak hashing) | NFR7.7 |

## Requirements

| ID | Requirement | Pass/fail check | Source |
|----|-------------|-----------------|--------|
| NFR7.1 | A `pre-commit` configuration runs gitleaks on every commit and blocks a commit containing a secret-shaped string. | AC0.1.3: a test commit with a fake AWS-key-shaped string is blocked | [Q3] [project Mandated] |
| NFR7.2 | CI runs gitleaks over the pushed commits as a backstop; a finding fails the run. | CI job fails on a seeded fake secret in a test branch | [Q3] |
| NFR7.3 | GitHub secret scanning (with push protection where the plan allows) is enabled on the repository. The repository contains no `.env` files; a `.env.example` lists variable names only, and `.env` is gitignored. | Repository settings checked; `git ls-files` shows no `.env` | [Q3] [construction rules] |
| NFR7.4 | Python and JS dependencies are pinned by committed lockfiles (`uv.lock`, `package-lock.json`); CI installs with frozen or locked mode and fails if a lockfile is out of date. | CI uses `uv sync --locked` and `npm ci`; an edited manifest without a lockfile update fails | [Q1] [practices] |
| NFR7.5 | CI runs `pip-audit` (against the uv-exported requirements) and `npm audit --audit-level=high`; any High or Critical vulnerability fails the run, and Medium and Low are reported in the log. Dependabot is configured for pip/uv, npm, and GitHub Actions on a weekly schedule. | AC0.1.2: a pinned known-vulnerable test version fails CI | [Q4] |
| NFR7.6 | CI workflows declare least-privilege `permissions:` (default `contents: read`), pin third-party actions to a full commit SHA, and run no `pull_request_target` with checkout of untrusted code. | Workflow lint/grep check in CI; review at the gate | [security-guide] |
| NFR7.7 | Ruff runs with the `S` (bandit-equivalent) rule set on all Python code; any finding fails the run unless it is suppressed inline with a justification comment. ESLint runs with a security-focused plugin set on TypeScript. | CI lint job | [practices] |
| NFR10.1 | CI enforces an 80% line-coverage floor separately for Python (`--cov-fail-under=80`) and TypeScript (Vitest coverage thresholds). | AC0.1.1 | [practices] |
| NFR10.2 | CI runs formatting checks (Black, Prettier) and type checks (mypy or pyright on public analysis functions, `tsc --noEmit` with `strict`); any failure blocks merge. | CI jobs | [practices] |
| NFR10.3 | Pre-merge CI finishes in 10 minutes or less (wall clock, cached) on the fixture-only suite. Performance ([P]) checks and `evaluation`-marked tests are excluded by default. | CI run duration in the Actions log | [Q5] [stories AC6.5.3] |

## Data Classification

U1 handles no user or personal data. The only sensitive items are secrets
(Restricted), and U1's controls exist to keep them out of the repository.

## Out of Scope for U1

- DAST, runtime rate limiting, and input validation belong to the runtime
  units U4 and U5.
- IaC scanning is deferred, because no infrastructure code exists before
  the separate deployment decision [project Forbidden].

## Sources

- [requirements] `inception/requirements-analysis/requirements.md` (NFR7, NFR10)
- [stories] `inception/user-stories/stories.md` (US0.1, AC6.5.3)
- [practices] `inception/practices-discovery/team-practices.md`
- [project Mandated] / [project Forbidden] `aidlc/spaces/default/memory/project.md`
- [construction rules] `aidlc/spaces/default/memory/phases/construction.md` (no hardcoded secrets)
- [security-guide] `.claude/knowledge/aidlc-devsecops-agent/security-guide.md`
- [Q1]–[Q5] `construction/u1-repo-foundation/nfr-requirements/nfr-requirements-questions.md`

## Assumptions & Open Questions

- [assumption] GitHub push protection for secret scanning depends on the repository's visibility and plan; if unavailable, NFR7.1 and NFR7.2 remain the enforcing controls.
