---
id: plan
role: designer
purpose: Turn a chosen direction into a repo-aware plan or external-agent handoff decision.
inputs:
  - decision_or_target
outputs:
  - .session/decisions/dec_{topic}_plan.md
user_selectable_lenses:
  - iteration
  - expand
  - language
  - strategy
  - test
  - architecture
done_check:
  - sequence_is_executable
  - constraints_are_named
  - verification_is_defined
---

# Plan Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default: plan in chat, do not load templates, and do not write files.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize views in user-provided lens order, then converge.
- `Mode: persist`: write only the requested `.session/decisions/**` target.
- `Mode: execute`: not valid for this task; use `build` with an approved plan or use the external-agent path.

## When To Use

- Use when the target direction is chosen and the user needs repo-aware implementation steps, sequencing, or external-agent handoff.
- Use when a plan must name success criteria, allowed changes, do-not-touch areas, verification, and stop conditions before any write.

## Do Not Use When

- Do not use to invent the target direction; use `shape`.
- Do not use to judge whether a plan, target, code, or diff is good; use `review`.
- Do not use to implement the plan; use `build` or the external-agent path after review.
- Do not use to update formal docs; use `sync`.

## Expected Output

- `Mode: discuss`: a repo-aware plan with `Success Criteria`, `Allowed Changes`, `Do Not Touch`, and step-level `Verify`.
- `Mode: persist`: a `.session/decisions/**` plan or external-agent handoff decision.

## Task Boundary Check

Before planning, classify the request:

- `fits`: target direction is chosen and the user needs repo-aware implementation steps or handoff.
- `fits_with_preflight`: plan depends on current code, formal docs, or session context. In `Mode: discuss`, perform read-only target-to-repo fit first.
- `wrong_task`: target direction is not chosen; recommend `shape`.
- `wrong_task`: user asks whether current implementation or target is reasonable; recommend `review`.
- `composite`: user asks to implement from target docs and current code; recommend `plan -> review -> external-agent/build -> review`.
- `missing_prerequisite`: `Mode: persist` lacks a `.session/decisions/**` target.

Plan may identify blockers and conflicts, but must not invent a new target. If target and repo conflict, recommend `review` or `shape`.

## Persist Templates

- Default: `.workflow/templates/decision.md`
- Plan detail: `.workflow/templates/plan.md`
- With `expand`: `.workflow/templates/expanded.md`

## Copilot Add Context

Required:

- #.workflow/tasks/plan.md
- relevant `.session/goal/*`, `.session/notes/**`, `.session/decisions/**`, docs, and target source files

For `Mode: persist`:

- Add #.workflow/templates/decision.md or #.workflow/templates/plan.md.
- Provide `Target: .session/decisions/dec_{topic}_plan.md`.

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Write the smallest repo-aware plan or handoff another engineer, Codex, Copilot, OpenCode, or similar agent can execute without making product decisions. Include target files, success criteria, allowed changes, do-not-touch areas, step-level verification, rollback or recovery notes, stop conditions, and target docs affected.

Every major step must include a verification method. If the verification method is unclear, mark the plan as not ready and recommend `review` or `shape` instead of treating it as approved.

## Output Rules

- Default persisted path: `{WorkflowRoot}/.session/decisions/dec_{topic}_plan.md`.
- External-agent handoff is also a session decision; keep it under `.session/decisions/**`.
- Formal docs updates go through `sync`.

## User Input

{{decision, target design, repo context, or implementation planning request}}
