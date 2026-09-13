**Collaborator:** aidlc-devsecops-agent

## Contribution

Scope of this review: lint/format rules, SAST/DAST, secret and dependency
scanning, and supply-chain controls, assessed against a greenfield
solo-founder MVP with no regulatory requirements identified
(`constraint-register.md` RC-1: no accounts, payments, or personal/health
data planned) and a confirmed cost-minimization constraint (BC-1). My
recommendations are sized to that context, not to an enterprise baseline —
this project should not carry paid SAST/DAST tooling or a security team's
process overhead, but it also should not ship with zero automated security
controls, especially since the repository will likely be public (a
portfolio/resume artifact recruiters will browse) and will eventually
handle a real AWS account (BC-2).

**1. Lint/format rules — draft is sound, one low-cost addition recommended.**
The draft's Code Style section (Black/Ruff for Python, Prettier/ESLint for
JS/TS, pre-merge enforcement) is appropriate and needs no correction. One
free, zero-new-tool addition: Ruff already ships a security-lint ruleset
equivalent to `bandit` (rule codes prefixed `S` — hardcoded-password
detection, unsafe `eval`/`subprocess` usage, weak crypto/hash calls, etc.).
Enabling that ruleset in the existing `pyproject.toml` Ruff config costs
nothing extra to run (same tool, same pre-merge check) and gives the
Python analysis layer a lightweight SAST pass for free. I recommend this
folded into the Code Style section rather than treated as a separate
tooling decision.

**2. SAST — draft is silent; a free, right-sized option exists.**
Dedicated SAST platforms (CodeGuru Security, SonarQube) are not justified
at this scope or budget. However, if the repository is hosted on GitHub
(likely, given the portfolio framing), GitHub CodeQL is free for public
repositories and requires only a workflow file — no new vendor, no cost,
consistent with BC-1. Recommend the Ruff security ruleset (item 1) as the
Python-layer baseline SAST, with CodeQL as a free addition once the repo's
hosting is confirmed. Neither is a hard requirement for this stage's
interview, but the gap should not be left completely unaddressed in
`team-practices.md`.

**3. DAST — correctly out of scope for now, but should be named as
deferred rather than silently absent.** No live endpoint exists yet;
`discovered-rules.md` already forbids provisioning cloud resources before
Unit-of-Work approval (BC-2). DAST has nothing to scan until a deployed
target exists. Recommend a one-line note in the Deployment section stating
DAST consideration is deferred to the deployment/infrastructure stage
that follows Unit-of-Work approval, so it is a recorded deferral rather
than an apparent gap.

**4. Secret scanning — this is the most significant gap in the draft and
should be corrected, not just supplemented.** Neither `team-practices.md`
nor `discovered-rules.md` mentions secret scanning or credential handling,
despite the project's eventual real AWS account (BC-2) and likely
third-party API keys for light-curve data sources (per the project.md
learning that the real data source is still an open assumption). The
construction-phase guardrail already mandates "never hardcode credentials,
API keys, or secrets," but that guardrail applies only once code
generation begins — it does not by itself guarantee an accidental commit
is caught before it reaches a public repository. For a solo builder with
no second reviewer to catch a leaked key in review, automated detection is
the only backstop. Concretely, recommend:
- A `.gitignore` covering `.env` and any local credential files from the
  first commit, not added reactively.
- Either GitHub secret scanning (free, automatic on public GitHub repos)
  or a pre-commit hook using a scanner such as gitleaks (free, single
  binary, no service dependency) — either satisfies this at zero cost.
This should be promoted to `discovered-rules.md` as a `## Mandated` rule
(see Positions) rather than left as a Code Style suggestion, because it is
a hard constraint with a real consequence (public credential leak) rather
than a stylistic preference the interview should re-litigate.

**5. Dependency vulnerability scanning — draft is silent; recommend a
free, ecosystem-native default.** Given BC-1 (cost minimization), paid
tools (Snyk, Amazon Inspector) are not justified. If hosted on GitHub,
Dependabot alerts (and optionally auto-updates) are free and require no
new tooling decision beyond enabling the feature. As a CI-runnable
fallback that doesn't depend on hosting choice, `pip-audit` (Python) and
`npm audit` (JS/TS) can run in the same pre-merge check already described
in Code Style, at no additional cost. Recommend adding this to Code Style
or Testing Posture's CI-gate description — either integration point works,
lead's choice.

**6. Supply-chain controls — draft is silent; recommend a minimal,
proportionate addition.** Two low-effort controls are appropriate at this
scope: (a) commit a lockfile (`requirements.txt` with pinned versions, or
a `poetry.lock`/`uv.lock`; `package-lock.json` for JS/TS) so builds are
reproducible and a compromised transitive dependency can't silently
change underneath the project; (b) since TC-4 requires the AI's analysis
be backed by real signal-processing/astronomy libraries the AI calls, take
care that those libraries (e.g., astronomy/signal-processing packages)
are installed from the official PyPI names rather than a similarly-named
typosquat — a one-time verification at dependency-selection time, not an
ongoing process. Neither needs a new tool or cost; both fit naturally
under Code Style.

**Net assessment:** the draft's lint/format coverage is correct and
sufficient. The larger gap is that SAST, secret scanning, dependency
scanning, and supply-chain controls are entirely unaddressed in both
`team-practices.md` and `discovered-rules.md`. All of my recommendations
are free or already-owned-tool extensions consistent with BC-1 and the
solo-builder context (OC-1) — none require a new paid vendor, a security
team, or process overhead disproportionate to an MVP. Secret scanning
(item 4) is the one item I'd escalate from "nice to have" to "mandated,"
given the public-repo/real-credentials combination and the absence of a
second human reviewer to catch a leak.

## Positions
- AGREE: The Code Style section's per-language lint/format tooling choices (Black/Ruff, Prettier/ESLint) and pre-merge enforcement — appropriate and requires no correction.
- AGREE: Treating full SAST/DAST platforms and paid dependency-scanning vendors as out of scope for this MVP's cost and team-size constraints (BC-1, OC-1) — enterprise tooling would be disproportionate here.
- OBJECT: No secret-scanning control or credential-handling practice appears anywhere in the draft, despite a real future AWS account (BC-2) and a public portfolio repository — this should be added as a `## Mandated` rule in `discovered-rules.md` (e.g., "ALWAYS run automated secret scanning — GitHub secret scanning or a pre-commit gitleaks hook — before any commit reaches the shared/public repository"), not left as an optional Code Style suggestion, because a leaked credential has a real, hard-to-reverse consequence and no second reviewer exists to catch it in review.
- OBJECT: Dependency vulnerability scanning and basic supply-chain controls (lockfiles, verified package names) are entirely absent from the draft — recommend adding a free, ecosystem-native default (Dependabot alerts and/or `pip-audit`/`npm audit` in the existing pre-merge check) to Code Style or Testing Posture so dependency risk isn't left completely unmanaged for a project that will eventually touch a real cloud account.
