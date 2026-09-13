<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is kept up to date automatically while the stage runs. Add observations at the review step, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->
- 2026-09-13T21:13:06Z — the user did not select "evaluation view" as a Must Have UI screen even though evaluation-against-KOI-labels was already a confirmed success metric; interpreted this as the evaluation methodology remaining in scope without necessarily needing its own dedicated screen, rather than as dropping evaluation altogether. Flagged explicitly in both the scope document and intent backlog so it isn't silently lost.

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->
- 2026-09-13T21:13:06Z — value-first sequencing (Q4) was confirmed alongside a specific backend-before-frontend dependency (Q5); resolved by keeping value-first as the overall Bolt-sequencing heuristic while carrying the one hard dependency forward as an explicit ordering constraint rather than treating the two answers as in tension.

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
