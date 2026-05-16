# Lens: Consistency

Use this lens when code, docs, code-adjacent README files, tests, or design intent may disagree.

## Add Only When Needed

- The user asks for document-to-document or document-to-code review.
- `.docs/current/**`, `.docs/shared/**`, `src/**/README.md`, README files, tests, or source code may describe different behavior.
- The work needs a source-of-truth judgment before deciding whether to update docs, change code, clarify design, or ignore a low-value difference.

## Checks

- Compare claims across docs, code, tests, and design artifacts.
- Cite the exact sources used for each conflict.
- Decide the likely source of truth, or mark intent as ambiguous.
- Classify each conflict as `doc_stale`, `code_drift`, `docs_conflict`, `ambiguous_intent`, `intentional_exception`, or `low_value`.
- Recommend exactly one follow-up path: `sync`, `shape`, `plan -> build`, or `none`.
- Do not rewrite docs or code during review.

## Output Hints

- Use `.workflow/templates/consistency_review.md` with `review`.
- Use `sync` only for confirmed document drift.
- If code may be wrong, route to `plan -> build` instead of changing docs to match code.
- If intent is unclear, route to `shape` before changing code or docs.
