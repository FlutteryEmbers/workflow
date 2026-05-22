---
id: sync
role: steward
purpose: Align formal docs and code-adjacent README files with confirmed decisions, code, or diffs.
inputs:
  - source_or_diff
outputs:
  - docs/{area}/{topic}.md
  - src/**/README.md
user_selectable_lenses:
  - consistency
  - distill
  - language
  - domain
  - architecture
done_check:
  - source_of_truth_is_named
  - formal_docs_rules_are_satisfied
  - stale_information_is_removed
---

# Sync Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/steward.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default: recommend sync targets in chat, do not load templates, and do not write files.
- `Mode: persist`: write only `docs/**` or explicit `src/**/README.md` targets.
- Reject `.session/**` targets; session files are maintained by the task that produced them.
- `Mode: execute`: not valid for this task.

## Task Boundary Check

Before syncing, classify the request:

- `fits`: user asks to align formal docs or code-adjacent README with confirmed source.
- `fits_with_preflight`: sync depends on checking source, existing docs, code, diff, consistency, or Formal Docs Rules. In `Mode: discuss`, perform read-only preflight first.
- `wrong_task`: user asks to save a session note; recommend `clarify` or `explore`.
- `wrong_task`: user asks to save a session decision, plan, or review; recommend `shape`, `plan`, or `review`.
- `wrong_task`: user asks to change code; recommend `plan -> build` or external-agent.
- `missing_prerequisite`: `Mode: persist` target is missing or is not `docs/**` or `src/**/README.md`.
- `missing_prerequisite`: source, target audience, source of truth, or Formal Docs Rules safety is unclear for `docs/**`.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Persist Templates

- Formal docs sync: `.workflow/templates/sync.md`
- Code-adjacent README targets: `.workflow/templates/code_readme.md`

## Copilot Add Context

Required:

- #.workflow/tasks/sync.md
- source `.session/**` decision or note, existing `docs/**`, code, diff, or code-adjacent README

For `Mode: persist`:

- Add #.workflow/templates/sync.md for `docs/**`.
- Add #.workflow/templates/code_readme.md for `src/**/README.md`.
- Provide `Target: docs/{area}/{topic}.md` or `Target: src/{area}/README.md`.

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Formal Docs Rules

Any write to `docs/**` must:

- Name source, target audience, and source of truth.
- Preserve stable, confirmed facts useful to the formal reader.
- Preserve existing docs structure and tone when updating an existing file.
- Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, internal redteam-only risks, temporary workarounds, sensitive implementation detail, and not-yet-announced plans.
- Output `docs blocked` and do not write `docs/**` when source, audience, source of truth, or safety is unclear.

## Instructions

Use `sync` only for formal docs and code-adjacent README alignment. Inputs may come from `.session/**`, source code, diffs, tests, or existing docs, but outputs are limited to `docs/**` and `src/**/README.md`.

## Output Rules

- Formal docs go to `{WorkflowRoot}/docs/**`.
- Code-adjacent README files go to `src/**/README.md`.
- Do not write `.session/**`.
- With `consistency`, sync only confirmed drift. If code may be wrong, route to `plan -> build` or external-agent implementation; if intent is unclear, route to `shape`.
- With `architecture`, formal constraints go to `docs/architecture/boundaries.md` or a user-specified docs target.
- With `language`, stable terminology goes to `docs/glossary.md` or a user-specified docs target.
- With `distill`, stable reference-derived structures go to formal docs only after they are accepted as project knowledge.

## User Input

{{formal doc update, code-adjacent README request, diff, session decision, or docs/code drift concern}}
