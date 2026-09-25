# Security Design — u1-repo-foundation

U1 is the supply-chain and quality gate for the whole repository, with no
runtime. This design defines where each control from
`security-requirements.md` runs, and how each control is proven to work.

## Defense in Depth: Where Each Control Runs

```
 developer machine            GitHub (push / PR)                     main
 ─────────────────            ──────────────────────────────         ─────────────
 pre-commit hooks      ──▶    CI: python │ frontend │ security  ──▶  branch protection
 (gitleaks, ruff,             + gate-self-test (config change /       (required checks,
  black, prettier,              weekly)                               linear history,
  eslint)                     + GitHub secret scanning                no force-push)
                              + Dependabot (weekly PRs)
```

Every control has at least two layers. Secrets are caught by the pre-commit
hook, the CI gitleaks step, and GitHub secret scanning. Quality is enforced
by the local hooks, then CI, then branch protection.

## Control Design

| Requirement | Design | Proof it works (automated) |
|-------------|--------|----------------------------|
| NFR7.1 pre-commit secret scan | `.pre-commit-config.yaml` with a gitleaks hook (version pinned), installed via `pre-commit install`; the README states it as a required setup step | `gate-self-test` job runs gitleaks on a temp file holding a runtime-generated fake key and asserts a non-zero exit |
| NFR7.2 CI secret scan | `security` job runs gitleaks over the push or PR commit range with the default ruleset plus a project `.gitleaks.toml` (allowlist only for documented test fixtures) | Same self-test, run in CI |
| NFR7.3 no committed secrets / `.env` | `.gitignore` covers `.env*` except `.env.example`; `.env.example` holds names only; GitHub secret scanning enabled in repo settings | `security` job fails if `git ls-files` lists any `.env` file other than `.env.example` |
| NFR7.4 locked dependencies | CI installs with `uv sync --locked` and `npm ci`; both fail on lockfile drift | `python` / `frontend` jobs themselves |
| NFR7.5 vulnerability threshold | `security` job: `uv export` → `pip-audit` with severity filtering (fail on High/Critical); `npm audit --audit-level=high`; Dependabot config for `uv`/pip, `npm`, `github-actions`, weekly | `gate-self-test` pins, in a temp project, a package version with a published High-severity advisory and asserts the audit step fails (closes review R-02: the fixture's severity matches the threshold) |
| NFR7.6 workflow hardening | Top-level `permissions: contents: read` in every workflow; third-party actions pinned by full SHA; no `pull_request_target` | `security` job runs actionlint plus a pinning check that fails on any `uses:` not pinned to a 40-character SHA (closes review R-03: fully automated, no human-review step) |
| NFR7.7 insecure code patterns | Ruff with `S` rules in `pyproject.toml`; inline `# noqa: S###` requires a justification comment; ESLint security plugin set for TS | `python` / `frontend` lint steps |
| NFR10.1 coverage floors | `pytest --cov --cov-fail-under=80` per Python package; Vitest `coverage.thresholds.lines: 80` | `python` / `frontend` test steps |
| NFR10.2 format and type checks | Black `--check`, Prettier `--check`, mypy (public analysis functions), `tsc --noEmit` (`strict`) | `python` / `frontend` jobs |
| NFR10.3 CI ≤ 10 min | Three parallel jobs with uv and npm caches; pytest default deselects `evaluation` and `perf` markers; performance ([P]) checks and evaluation runs live in separate, non-required workflows | Workflow run duration; a non-required `ci-duration` check warns above 8 minutes |
| (branch integrity) | Branch protection on `main`: `python`, `frontend`, `security` required; linear history; no force-push or deletion. `gate-self-test` is required only on PRs that touch `.github/` or hook config | GitHub settings; the solo author can merge their own PR once checks are green |

## Gate Self-Test Job (design)

- **Trigger**: pull requests that change `.github/**`,
  `.pre-commit-config.yaml`, `.gitleaks.toml`, or dependency-audit config,
  plus a weekly schedule.
- **Isolation**: all known-bad inputs are generated in a temp directory at
  runtime and never committed. The fake secret uses a documented test
  pattern that is not a real credential.
- **Assertions**: each scanner must exit non-zero on its bad input. The job
  fails if any scanner stops detecting, which catches a silently broken
  gate.

```text
# illustrative only
tmp=$(mktemp -d); echo "$FAKE_KEY_PATTERN" > "$tmp/leak.txt"
! gitleaks detect --no-git --source "$tmp"          # must fail → job passes
```

## Residual Risks

| Risk | Treatment |
|------|-----------|
| A secret is committed with hooks bypassed (`--no-verify`) and pushed | Caught by CI gitleaks and GitHub secret scanning; the response is to revoke and rotate immediately, then purge history (runbook note in README) |
| A zero-day in a dependency not yet in the advisory databases | Accepted; Dependabot and the weekly audit re-run reduce the time it stays exposed |
| Branch protection needs repo admin settings that cannot be set in code | A setup checklist in README; verified once when the repository is created |

## Sources

- [nfr-req] `construction/u1-repo-foundation/nfr-requirements/security-requirements.md`, `tech-stack-decisions.md`
- [review] U1 NFR Requirements review record (findings R-02, R-03)
- [practices] `inception/practices-discovery/team-practices.md`
- [devsecops] `.claude/knowledge/aidlc-devsecops-agent/devsecops-pipeline-patterns.md`
- [Q1]–[Q3] `construction/u1-repo-foundation/nfr-design/nfr-design-questions.md`

## Assumptions & Open Questions

- [assumption] `pip-audit` severity filtering is available in the pinned version, or is applied by post-processing its JSON output against advisory severity. Code Generation picks one.
- Review R-01 from NFR Requirements (partial coverage of NFR7 input validation) is out of U1's scope. Input validation is designed in U4 and U5.
