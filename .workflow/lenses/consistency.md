# Lens: Consistency

Use this lens when session decisions, project docs, code, tests, or code-adjacent README files may disagree and the user needs a source-of-truth or follow-up action judgment.

Do not use this lens by default for discovery questions. If the user asks what exists, where it is, how it works, or how reliable the evidence is, use `explore` and record mismatches as reliability risks. Use `consistency` only when the user asks whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth.

## Conflict Types

- `doc_stale`: project docs or README are outdated.
- `code_drift`: code diverged from accepted intent.
- `docs_conflict`: project docs disagree with each other.
- `session_conflict`: session decisions disagree or supersede each other.
- `ambiguous_intent`: source of truth is unclear.
- `intentional_exception`: difference is deliberate and should be noted.
- `low_value`: difference is not worth changing.

## Recommended Actions

- `sync`: confirmed project docs or code-adjacent README drift.
- `shape`: unclear product, design, or architecture intent.
- `plan -> build`: code likely diverged from accepted behavior.
- `external-agent`: native Plan/Implement is appropriate for the fix.
- `none`: accepted exception or low-value difference.

## Output Hints

- Reviews can be saved with `save` as `.session/drafts/review_{topic}.md`.
- `sync` may write only `docs/**` or `src/**/README.md`.
- Do not silently rewrite project docs to match code when code may be wrong.
