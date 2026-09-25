# Discovered Rules — OrbitSleuth

## Mandated

- ALWAYS require every AI-generated hypothesis to cite the specific evidence supporting it and to state a confidence/uncertainty level, never a bare verdict (confirmed hard constraint, `intent-statement.md` TC-5).
- ALWAYS require explicit human accept/reject/annotate action on an AI-generated hypothesis before it is treated as final, and allow the human to intervene at any point in the investigation (confirmed hard constraint, `intent-statement.md` TC-6).
- ALWAYS back AI analysis with real, tool-invoked signal-processing/astronomy analysis (e.g. period-detection, detrending, vetting statistics) rather than free-form LLM reasoning (confirmed hard constraint, `intent-statement.md` TC-4).
- ALWAYS run automated secret scanning — GitHub secret scanning or a pre-commit gitleaks hook — before any commit reaches the repository (confirmed hard constraint, interview Q11/Q12; escalated from a Code Style suggestion to Mandated on the devsecops agent's explicit recommendation, since a leaked credential is a real, hard-to-reverse consequence and there is no second reviewer to catch it).

## Forbidden

- NEVER provision cloud resources or begin a deployment build-out before a separate Unit-of-Work approval is given (confirmed hard constraint, `intent-statement.md`, `constraint-register.md` BC-2).
- NEVER let the AI unilaterally declare a "discovery" — its role is advisory only (confirmed hard constraint, `intent-statement.md`).
