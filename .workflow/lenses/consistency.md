# Lens: Consistency

Use this lens for triangulation review when session decisions, project docs, code, tests, plans, README files, workflow artifacts, prompts, templates, or archive summaries may disagree and the user needs a source-of-truth or follow-up action judgment.

Do not use this lens by default for discovery questions. If the user asks what exists, where it is, how it works, or how reliable the evidence is, use `explore` and record mismatches as reliability risks. Use `consistency` only when the user asks whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth.

## Triangulation Points

Check the relevant points and name missing evidence:

- Intent / Decision Source: user decision, shape, plan, review, session decision, ADR, or project docs.
- Implementation Source: code, tests, runtime behavior, config, diff, or build output.
- Artifact Source: project docs, README, session thread, archive summary, prompt, template, workflow task, or adapter.

Not every review has all three points. If a point is missing and affects the verdict, classify source of truth as unclear and route to `shape` or `review` instead of projecting changes.

## Task Responsibilities

- `review` owns verdicts: aligned, drift found, source of truth unclear, needs shape, needs plan, project-docs sync needed, archive sync needed, code build needed, or none.
- `shape` owns source-of-truth, artifact-boundary, and long-term ownership decisions when intent is unclear.
- `plan` owns repair sequencing after a verdict or decision is confirmed.
- `sync` only projects confirmed outcomes to stable documents: `project-docs` or `session-archive`.
- `persist` owns active session artifacts and must not use this lens to resolve source-of-truth conflicts.
- `explore` may record conflicts as reliability notes, but must not judge which source is correct.

## Conflict Types

- `doc_stale`: project docs or README are outdated.
- `code_drift`: code diverged from explicit project intent.
- `docs_conflict`: project docs disagree with each other.
- `session_conflict`: session decisions disagree or supersede each other.
- `thread_superseded`: active thread content was superseded by a later thread artifact or explicit decision.
- `archive_stale`: archive summary no longer matches the source thread or synced project docs.
- `prompt_template_drift`: prompts, templates, tasks, or adapters use conflicting workflow semantics.
- `source_of_truth_missing`: no source is authoritative enough to project or repair from.
- `ambiguous_intent`: source of truth is unclear because product, architecture, or workflow intent is unsettled.
- `intentional_exception`: difference is deliberate and should be noted.
- `low_value`: difference is not worth changing.

## Recommended Actions

- `plan -> sync project-docs`: confirmed project docs or code-adjacent README drift.
- `sync session-archive`: completed thread needs an archive summary and prerequisites are clear.
- `shape`: unclear product, design, architecture, source-of-truth, or artifact-boundary intent.
- `plan -> build`: code or workflow artifacts likely diverged from intended behavior.
- `external-agent`: native Plan/Implement is appropriate for the fix.
- `persist`: active session artifact should record the verdict or unresolved conflict.
- `none`: explicit exception or low-value difference.

## Output Hints

For reviews, include:

```text
Triangulation Map:
| Claim | Intent Source | Implementation Source | Artifact Source | Status | Confidence |
| :--- | :--- | :--- | :--- | :--- | :--- |

Conflict Classification:
- <type>: <evidence and impact>

Verdict:
- aligned | drift found | source-of-truth unclear | needs shape | needs plan | project-docs sync needed | archive sync needed | code build needed | none

Recommended Action:
- <shape | plan -> sync project-docs | sync session-archive | plan -> build | persist | none>
```

Reviews can be persisted with `persist` as `.session/threads/{thread}/review_{topic}.md`.

`sync` may write only stable-document targets for its selected domain: `docs/**`, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md`.

Do not silently rewrite project docs to match code when code may be wrong. Do not write archive summaries that present unresolved thread conflicts as settled conclusions.
