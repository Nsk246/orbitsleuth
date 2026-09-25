# NFR Design — u1-repo-foundation — Questions

## Sources

- [nfr-req] `construction/u1-repo-foundation/nfr-requirements/security-requirements.md` (NFR7.1–NFR7.7, NFR10.1–NFR10.3), `tech-stack-decisions.md`
- [review] NFR Requirements review for U1: R-02 (the AC0.1.2 fixture must be High or Critical severity to match the NFR7.5 threshold) and R-03 (NFR7.6 mixes an automated check with human review)
- [practices] `inception/practices-discovery/team-practices.md` (solo, short-lived branches squash-merged to `main`)

U1 is a packaging unit, so only the security design applies here. There is
no runtime to scale, cache, or observe.

## Q1. Should `main` be protected so that only green CI can merge?

- A. Yes: branch protection on `main` requires all CI checks to pass, blocks force-pushes and deletions, and requires linear history (fits squash-merge). The solo author can still merge their own pull requests
- B. No protection; rely on running checks before each merge
- X. Other (please specify)

[Answer]: A (2026-09-25T15:16:47Z, **Mode:** chat — user accepted the recommended option: "All good")

## Q2. How should the CI jobs be laid out?

The time limit is 10 minutes (NFR10.3).

- A. Parallel jobs with dependency caching: `python` (format, lint, type-check, tests + coverage), `frontend` (format, lint, type-check, tests + coverage), `security` (gitleaks, `pip-audit`, `npm audit`, workflow check)
- B. One sequential job
- X. Other (please specify)

[Answer]: A (2026-09-25T15:16:47Z, **Mode:** chat — user accepted the recommended option: "All good")

## Q3. How do we prove the security gates actually fail when they should?

This resolves review findings R-02 and R-03: every gate gets an automated,
repeatable proof instead of a one-off manual check.

- A. A "gate self-test" CI job that generates known-bad inputs at runtime (a fake secret-shaped string, and a dependency spec pinned to a version with a known High-severity advisory) and asserts that gitleaks and the audit step report them. It runs on changes to CI config and weekly, never committing a real secret. The NFR7.6 workflow rules (SHA-pinned actions, least-privilege permissions) are checked by an automated workflow linter (for example actionlint plus a pinning check), with no human-review step
- B. Verify each gate once by hand and document it
- X. Other (please specify)

[Answer]: A (2026-09-25T15:16:47Z, **Mode:** chat — user accepted the recommended option: "All good")

## Assumptions & Open Questions

None.

## Consolidated Summary Confirmation

- Branch protection on `main`: required CI checks, no force-push or deletion, linear history (Q1: A)
- CI as parallel `python`, `frontend`, `security` jobs with dependency caching (Q2: A)
- Automated gate self-test job with runtime-generated known-bad inputs, plus an automated workflow linter for NFR7.6 (Q3: A)

Does this all look correct before I generate the security design for u1-repo-foundation?

- Looks correct
- Request changes

[Answer]: Looks correct
