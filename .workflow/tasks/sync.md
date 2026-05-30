---
id: sync
role: steward
purpose: Project confirmed decisions, code, diffs, tests, or existing docs cleanup into code-aligned project docs and code-adjacent README files.
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
  - project_docs_rules_are_satisfied
  - stale_information_is_removed
---

# Sync Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/steward.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default: recommend sync targets in chat, do not load templates, and do not write files.
- `Mode: persist`: write only `docs/**` or explicit `src/**/README.md` targets.
- Reject `.session/**` targets; session files are maintained by `save`.
- Reject `notes/**` targets; disposable exploration notes are maintained by `save`.
- `Mode: execute`: not valid for this task.

## When To Use

- Use when confirmed decisions, code, diffs, tests, or existing docs cleanup need to be projected into code-aligned `docs/**` or `src/**/README.md`.
- Use after review/build/external-agent work when stable facts should become project execution context for future humans or agents.

## Do Not Use When

- Do not use to maintain `.session/**`; use `save`.
- Do not use to maintain `notes/**`; use `save`.
- Do not use to decide whether code or docs are correct, acceptable, or source of truth; use `review`.
- Do not use to change source code; use `plan -> build` or the external-agent path.

## Expected Output

- `Mode: discuss`: proposed sync target, source of truth, future-use success criteria, blockers, and next prompt.
- `Mode: persist`: updates only to `docs/**` or explicit `src/**/README.md` targets.

## Task Boundary Check

Before syncing, classify the request:

- `fits`: user asks to align code-aligned project docs or code-adjacent README with confirmed source.
- `fits_with_preflight`: sync depends on checking source, existing docs, code, diff, consistency, future-use success criteria, or Project Docs Rules. In `Mode: discuss`, run default implicit preflight first.
- `wrong_task`: user asks to save any session artifact; recommend `save`.
- `wrong_task`: user asks to change code; recommend `plan -> build` or external-agent.
- `missing_prerequisite`: `Mode: persist` target is missing or is not `docs/**` or `src/**/README.md`.
- `wrong_task`: target is `notes/**`; recommend `save` because exploration notes are not project docs.
- `missing_prerequisite`: source, future use, source of truth, or Project Docs Rules safety is unclear for `docs/**`.
- `missing_prerequisite`: source is only `.session/drafts/**` and the user has not explicitly confirmed it as source of truth.

Default implicit preflight runs only in `Mode: discuss` and checks source, target, future use, source of truth, existing docs structure, and Project Docs Rules. In `Mode: persist`, do not preflight; block with `docs blocked` when any project docs prerequisite is missing.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Persist Templates

- Project docs sync: `.workflow/templates/sync.md`
- New project doc target: `.workflow/templates/project_doc.md`
- New architecture note target: `.workflow/templates/architecture_note.md`
- Code-adjacent README targets: `.workflow/templates/code_readme.md`

## Copilot Add Context

Required:

- #.workflow/tasks/sync.md
- source `.session/accepted/**` decision, plan, or review by default; existing `docs/**`, code, diff, tests, or code-adjacent README as needed
- `.session/drafts/**` only when the user explicitly confirms it as source of truth

For `Mode: persist`:

- Add #.workflow/templates/sync.md for the sync operation.
- Add #.workflow/templates/project_doc.md or #.workflow/templates/architecture_note.md only when creating a new `docs/**` target.
- Add #.workflow/templates/code_readme.md for `src/**/README.md`.
- Provide `Target: docs/{area}/{topic}.md` or `Target: src/{area}/README.md`.

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Project Docs Rules

Any write to `docs/**` must:

- Name source, future use, and source of truth.
- Name future-use success criteria for the target document.
- Preserve stable, confirmed facts useful for future human or agent execution.
- Preserve existing docs structure and tone when updating an existing file.
- Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, temporary PoC details, low-level implementation mirror content, and details that would mislead future execution.
- Output `docs blocked` and do not write `docs/**` when source, future use, source of truth, future-use success criteria, or safety is unclear.

These Project Docs Rules are built-in safety, not the `redteam` lens. Do not run full critique during `sync`; recommend `review --lens redteam` only when project docs content exposes unresolved risk, sensitive assumptions, or high-cost commitments.

## Create Docs Gate

Create a new `docs/**` file only when it would reduce future human or agent execution mistakes. A new project doc is appropriate when at least one is true:

- The knowledge affects future code changes.
- The boundary or design intent is not obvious from code.
- The area is a stable public surface, architecture boundary, or repeated execution context.
- The same confusion, drift, or planning discussion has appeared more than once.
- The source is `.session/accepted/**` or a clear code/diff/test fact.

If the content is temporary, exploratory, not accepted, already obvious from code, or unlikely to affect future execution, output `docs blocked` and recommend `save` to `.session/**` or explicit `notes/**`.

## Instructions

Use `sync` only for code-aligned project docs and code-adjacent README projection. Inputs may come from `.session/**`, source code, diffs, tests, or existing docs, but outputs are limited to `docs/**` and `src/**/README.md`.

Prefer `.session/accepted/**` as the session-level input source. If the only available source is `.session/drafts/**`, `.session/inbox/**`, or `notes/**`, block until the user explicitly confirms that source of truth or routes it through `review` and `save` into `.session/accepted/**`.

`review` makes the judgment; `sync` performs the projection. If the request asks which source is correct, whether the design is good, whether code should change, or whether docs should override code, route to `review` or `shape` instead of deciding inside `sync`.

## Output Rules

- Project docs go to `{WorkflowRoot}/docs/**`.
- Code-adjacent README files go to `src/**/README.md`.
- Do not write `.session/**`.
- Do not write `notes/**`.
- Default project docs source from session is `.session/accepted/**`; drafts, inbox notes, and exploration notes require explicit source-of-truth confirmation.
- With `consistency`, sync only confirmed drift. If code may be wrong, route to `plan -> build` or external-agent implementation; if intent is unclear, route to `shape`.
- With `architecture`, project constraints go to `docs/architecture/boundaries.md` or a user-specified docs target.
- With `language`, stable terminology goes to `docs/glossary.md` or a user-specified docs target.
- With `distill`, stable reference-derived structures go to project docs only after they are accepted as project knowledge.

## User Input

{{project doc update, code-adjacent README request, diff, session decision, or docs/code drift concern}}
