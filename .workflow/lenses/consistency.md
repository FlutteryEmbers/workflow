# Lens: Consistency

Use this lens when session decisions, formal docs, code, tests, or code-adjacent README files may disagree.

## Conflict Types

- `doc_stale`: formal docs or README are outdated.
- `code_drift`: code diverged from accepted intent.
- `docs_conflict`: formal docs disagree with each other.
- `session_conflict`: session decisions disagree or supersede each other.
- `ambiguous_intent`: source of truth is unclear.
- `intentional_exception`: difference is deliberate and should be noted.
- `low_value`: difference is not worth changing.

## Recommended Actions

- `sync`: confirmed formal docs or code-adjacent README drift.
- `shape`: unclear product, design, or architecture intent.
- `plan -> build`: code likely diverged from accepted behavior.
- `external-agent`: native Plan/Implement is appropriate for the fix.
- `none`: accepted exception or low-value difference.

## Output Hints

- Reviews can be saved with `save` as `.session/drafts/review_{topic}.md`.
- `sync` may write only `docs/**` or `src/**/README.md`.
- Do not silently rewrite formal docs to match code when code may be wrong.
