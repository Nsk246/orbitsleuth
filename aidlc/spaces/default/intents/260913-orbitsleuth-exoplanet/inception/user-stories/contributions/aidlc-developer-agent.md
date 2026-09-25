**Collaborator:** aidlc-developer-agent

## Contribution

Angle: can one person build each story in about 1–3 days, and are the dependencies real? Most visitor-facing stories are sized correctly. The main risk is that the analysis engine, the curation pipeline and the wire contract have no story of their own. As drafted, their work is hidden inside US3.1 and US6.1, so those two stories are each 2–3 weeks of work, not 1–3 days.

### 1. Oversized stories: suggested splits

- **US3.1 is the whole engine plus the API plus trace replay.** To meet AC3.1.1–AC3.1.3 you need detrending, a BLS search, five vetting statistics, scoring and ranking, the output contract, an analyze endpoint and a replay UI. Suggested split (each piece about 2–3 days; engine pieces written fixtures-first per team.md):
  - **US3.0a Engine core (P3 enabler)**: detrend and BLS period search in a plain Python package. AC: an injected-signal fixture recovers its period within a stated tolerance. AC: same input and version gives identical output (moves AC3.1.3 here).
  - **US3.0b Vetting statistics (P3 enabler)**: depth, duration, SNR, odd/even and secondary-eclipse checks, each returning `pass`/`fail`/`not_run` plus a reason. This is the engine side of AC3.3.1.
  - **US3.0c Scoring, ranking and output contract (P3 enabler)**: a deterministic, documented rule-based function from vetting results to the four category scores. Move AC4.1.4 (the TC-5 contract test) and the TC-4 provenance assertion (AC4.2.2) here. The drafts never say where the scoring model is defined, yet AC3.3.2 and AC4.5.x both depend on it.
  - **US3.1 (slimmed)**: the analyze endpoint serves the stored result, the trace panel replays it, and the 10 s p95 applies. It keeps AC3.1.1 and AC3.1.2.
- **US6.1 has a circular dependency.** The draft says US6.1 feeds US1.1, but pre-computed results (AC6.1.1) need the full engine (US3.0a–c). Suggested split:
  - **US6.1a**: catalog manifest schema, provenance fields and a synthetic-curve generator (seeded).
  - **US6.1b**: fetch and store the real curves. This is blocked by the open data-source decision.
  - **US6.1c**: pre-compute results tagged with the pipeline version. This depends on US3.0c.
- **US6.2 is two stories.** Split it into:
  - **US6.2a**: frozen sample file plus a versioned KOI-to-category mapping. The mapping is still an open question in requirements.
  - **US6.2b**: run the harness and compute metrics.
  - Add to AC6.2.3: the input light curves are cached and checksummed. Otherwise archive reprocessing breaks "identical metrics", even with the same sample file.
- **US2.1 is at the top of the size range.** A 70k-point chart within 100 ms, keyboard zoom/pan and a screen-reader summary all together is about 3 days. Move AC2.1.3 into its own small story (US2.1b) if it slips.

### 2. Missing enabler work (add as stories or ACs)

- **E1 Repo scaffold and CI (P3, do first)**: GitHub Actions running Black/Ruff (including `S`), Prettier/ESLint, pytest-cov and Vitest with an 80% floor, pip-audit and npm audit, and the gitleaks pre-commit hook (Mandated). No story owns this today. With `skeleton: off`, it must still land before feature Bolts.
- **E2 Wire contract (FR4.8, Q8)**: a shared schema for catalog, curve, result, evidence, trace and error payloads. Python snake_case is translated to camelCase only in the API layer. AC: a contract test fails if the engine output and the API schema drift.
- **E3 Catalog and curve endpoints**: US1.1 and US2.1 need an API that serves the catalog and curve arrays, with IDs validated at the boundary (NFR7). Add an AC to US2.1 for payload size: 70k points as JSON is about 1.5–2 MB, so send them compactly or decimated.
- **E4 Data-source spike (P3)**: choose and verify the real-curve archive (the project.md correction requires web verification). This blocks US6.1b and US6.2. Timebox it to 1 day.
- **Run ID and structured logging (US6.5)**: AC3.4.4 logs every error "with the run ID", so this plumbing must exist before US3.4. Move US6.5 earlier, or make AC6.5.1's run-ID plumbing part of US3.1.

### 3. Ambiguous or infeasible acceptance criteria

- **AC3.3.2 (confidence rule)** is only half-defined. A check marked "not run" means different things for different hypotheses. For `eclipsing_binary`, an odd/even check *passing* lowers the score. Proposed rewording: "Given a hypothesis whose score uses check C, When C is `not_run`, Then that hypothesis's confidence is strictly lower than when C returns the result that supports it (all other inputs equal), and the result states the penalty, e.g. 'odd/even check not run: −N points'." This is testable as a property test on US3.0c.
- **AC4.5.1 ("clear" mismatch)** cannot be tested as written, and it cannot be guaranteed on real data. Proposed rewording: "Given a synthetic EB fixture with odd/even depth ratio ≥ R and/or secondary depth ≥ S·σ (R and S set in functional design), When analysis runs, Then `eclipsing_binary` ranks above `transit` and cites that check." Real-data EB behaviour belongs in the US6.2 metrics, not in an AC.
- **AC3.5.1 (rate-limit identity without accounts)**: "per visitor" can only be best-effort, keyed on a hashed client IP or an anonymous cookie. Both are easy to evade. Say this in the AC, and treat AC6.4.2's daily cap as the hard guard. Also specify:
  - Only *live* runs count toward the limit. Serving a pre-computed result (US3.1) never does.
  - Re-runs (US5.3) and requests for more evidence (US5.4) do count. Neither has a cached result, so AC3.5.3 is the normal path for them.
  - The counter store is in-process or a single local store for now (no cloud provisioning, BC-2).
- **AC6.4.2 (tracked spending)** needs a cost meter: estimated tokens × configured unit price per LLM call, with a UTC-day reset. Add that as an AC. Without it, "spending reaches cap" has nothing to measure.
- **AC4.3.1 and AC4.4.1–2 (explanation guard)** need a runtime check, not just a CI check. After generation, pull the numbers out of the text and compare them with tool outputs (tolerance: the displayed rounding). Also scan for forbidden terms. If either check fails, discard the explanation and fall back to AC4.3.2 ("unavailable").
- **AC4.3.3**: make it structural. Generate the explanation only after the ranked result is frozen, and test by hashing the result before and after.
- **AC5.1.3 (TC-6)**: review state lives only in the browser (FR6.6), so "any code path" really means the frontend review-state module. Add: "the API never returns a `final`/accepted status", and "only the Accept/Reject handlers can change status" (a unit test on the reducer, plus the integration test).
- **AC5.4.1 ("additional checks or an extended analysis")**: no concrete checks are named, so a developer cannot implement it. Name them now (e.g. harmonic check at P/2 and 2P, plus a wider period range), or lower it to Could Have until functional design lists them.
- **AC3.1.3 and AC6.2.3 ("identical")**: floating-point results can differ between platforms. State the scope: same pinned lockfile and platform, with scores compared at stored precision (e.g. 6 decimal places).
- **US2.2 and US2.3**: the frontend must not recompute anything, so the detrended series and the phase-fold come from the engine/API, not client code. US2.2 therefore also depends on US3.0a, and US2.3 needs the period and phase from the result payload.

### 4. Dependency corrections

- US3.5 depends on US6.4 (enforcement), which is not listed. AC3.5.1 and AC6.4.1 duplicate each other. Keep enforcement in US6.4 and only the visitor messaging in US3.5.
- US1.3 depends on US1.1, which is not listed. US1.1's INVEST note should say it is tested against a fixture catalog, so it does not wait on US6.1.
- AC3.2.3 (explanation step in the trace) needs the LLM explanation from US4.3. Either US3.2 depends on US4.3, or move AC3.2.3 into US4.3.
- US3.2 needs a transport choice for step-by-step updates (SSE, WebSocket or polling). Flag it for design. Polling is the cheapest option that fits the size budget.
- Proposed critical path: E1 → US3.0a → US3.0b → US3.0c → E2/E3 → US6.1a/c → US1.1 → US2.1 → US3.1 → US4.1 → US4.2 → US5.1. With the splits there are about 33 stories, slightly over the 20–30 in Q3. That is acceptable, because the extra stories surface real work rather than inflating the list.

## Positions

- AGREE: Q4 (failures as ACs within their happy-path story, plus dedicated US3.4). Keeps the list small and testable.
- AGREE: Three personas, with P3 as the actor for enabler work. That gives the engine and CI stories a legitimate actor.
- OBJECT: US3.1 as drafted. It hides the whole engine, scoring model and contract in one story that cannot be done in 1–3 days. Split it per §1.
- OBJECT: Critical path "US6.1 -> US1.1". Pre-computing results needs the engine, so the dependency runs the wrong way.
- OBJECT: AC4.5.1 and AC3.3.2 wording. As written they cannot be tested or are ambiguous. Rewrite them against synthetic fixtures and a monotonicity property.
- OBJECT: AC3.5.1 duplicating AC6.4.1 with no stated visitor identity. The rate-limit key and what counts as a run must be explicit, and best-effort must be acknowledged.
- OBJECT: US5.4 as Should Have with unnamed "additional checks". It cannot be implemented until the checks are named.
