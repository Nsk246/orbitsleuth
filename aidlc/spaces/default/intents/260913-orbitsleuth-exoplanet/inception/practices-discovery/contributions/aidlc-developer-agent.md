**Collaborator:** aidlc-developer-agent

## Contribution

Reviewed `team-practices.md`, `discovered-rules.md`, and `evidence.md` against
the developer's assessment scope (naming, layer boundaries, error handling,
file organization, code-style conventions), plus the scope document and
intent backlog for project shape (Python analysis backend, tool-backed
signal-processing engine, an API/service boundary, and a modern JS/TS
interactive frontend; three-layer flow implied by the backlog: data
ingestion → analysis engine → API/service boundary → frontend).

**1. Naming and cross-language consistency (gap).** The draft's Code Style
section correctly sets per-language idiomatic naming (snake_case Python,
camelCase JS/TS) but says nothing about the naming convention at the
boundary between them — the JSON shape the analysis/API layer emits and the
frontend consumes. Intent Backlog Capability 3/5 makes this an explicit
hard dependency ("the analysis backend's evidence + confidence output shape
must be defined before the frontend's hypothesis-display design"), so the
practice discovery interview should settle at least the convention (e.g.
API payloads serialize as camelCase regardless of the Python side's
internal snake_case, translated at the serialization boundary — a common
and low-friction default) rather than leaving each side to invent its own
mapping later. I recommend adding one bullet to Code Style: the field-name
casing convention used at the API boundary, and that hypothesis/evidence
vocabulary (`transit` / `stellar_activity` / `noise`, `confidence`,
`evidence[]`) is a single shared vocabulary, not independently named on
each side.

**2. Layer boundaries (partially addressed, should be made explicit).** The
Testing Posture section's ordering — "Python analysis functions, then
API/service boundary, then frontend components" — implicitly documents a
three-layer architecture, but this lives only inside the testing section.
Given the hard constraint (TC-4) that AI analysis must be backed by real
tool-invoked signal-processing rather than free-form reasoning, it is worth
stating directly in Code Style (not just inferred from test ordering) that:
the analysis engine is a plain, framework-independent Python package (no
API/web-framework imports inside it, so it stays independently testable and
reusable); the API/service layer is the only place that translates between
the analysis engine's internal representation and the wire format; and the
frontend never re-implements analysis/business logic (e.g. re-deriving a
confidence score client-side) — it only renders what the API returns. This
also protects the "tool-backed, not free-form" hard constraint by keeping
the analysis logic in one auditable place.

**3. Error handling (gap — not addressed anywhere in the draft).** Neither
`team-practices.md` nor `discovered-rules.md` says anything about error
handling, yet the Construction phase guardrails mandate error handling at
every integration boundary (API calls, external data fetch, file I/O) and
require distinguishing recoverable from fatal errors, with no silent
failures. This project has several real boundaries where this matters
concretely: fetching/parsing archival or synthetic light-curve data
(Capability 1), the tool-backed analysis calls (period-detection/
detrending — Capability 2, which can legitimately fail to converge or
receive malformed input), and the API layer that surfaces failures to the
frontend for a human reviewer. I recommend the interview add a short Code
Style (or new "Error Handling") note, e.g.: analysis-engine failures raise
typed/domain exceptions rather than returning ambiguous nulls; the API
layer catches these and returns a structured error response (distinct from
a low-confidence *hypothesis*, which is a valid result, not an error) so
the frontend can distinguish "analysis ran and found nothing conclusive"
from "analysis failed to run"; and no exception is swallowed silently
anywhere in the ingestion → analysis → API chain. This is a small addition
but directly protects the confirmed hard constraint (TC-6) that a human can
intervene at any point — a human can't intervene sensibly on a failure
that was silently swallowed.

**4. File organization (light gap, appropriate to flag but not over-specify
here).** The draft does not propose even a lightweight top-level layout
(e.g. a monorepo with clearly separated `backend/`/`analysis` and
`frontend` trees, with the analysis engine as its own importable
sub-package distinct from API route/handler code). Given this is a
solo-builder, portfolio-facing project, I would not over-specify exact
directory names at practices-discovery — that is better left to a design
stage — but the interview should at minimum confirm the top-level split
(single repo, backend and frontend as clearly separated top-level trees)
since it is a naming/organization decision that becomes expensive to
reverse once the first Bolt lands code.

**5. Type-hint / type-safety stance (gap).** Neither `org.md`'s Code Style
defaults nor the draft mention a type-hint or strictness policy for either
language. Given that analytical *correctness* is a stated success metric
(accuracy against the NASA KOI table) and this is a portfolio artifact
where code quality itself will be read, I'd suggest the interview confirm:
Python type hints on public analysis-engine functions (optionally checked
by `mypy`/`pyright` if the author wants that rigor), and TypeScript
`strict` mode on the frontend. This is a cheap practice to affirm now and
expensive to retrofit once several Bolts of code exist.

**Otherwise**, the draft's per-language tooling choices (Black/Ruff for
Python, Prettier/ESLint for JS/TS) are sound, consistent with the org
default, and require no change from a developer-conventions standpoint.

## Positions
- AGREE: Per-language formatter/linter choice (Black/Ruff for Python, Prettier/ESLint for JS/TS), deferring to project-level config per the org default — sound baseline that needs no rework.
- AGREE: The testing-ordering sequence (analysis functions → API/service boundary → frontend components) correctly reflects the project's real layering and is a good foundation to promote into an explicit layer-boundary statement (see Contribution point 2).
- OBJECT: No error-handling convention is stated anywhere in the draft, despite the Construction phase guardrails mandating error handling at every integration boundary and this project having several real ones (data ingestion, tool-backed analysis calls, API-to-frontend surfacing) — the interview should resolve this before integration.
- OBJECT: No API-boundary field-naming/casing convention or shared hypothesis/evidence vocabulary is stated, despite the backlog making the analysis-output shape a hard cross-layer dependency (Capability 3 before Capability 5) — this should be a Code Style bullet, not left implicit.
- OBJECT: No type-hint/strictness stance (Python type hints, TypeScript `strict`) is proposed, though it is a cheap-now/expensive-later decision directly relevant to the project's stated accuracy success metric.
