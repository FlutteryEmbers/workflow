---
id: route
role: analyst
purpose: Recommend the smallest useful next path.
inputs:
  - user_goal
outputs:
  - chat_route
user_selectable_lenses:
  - iteration
  - expand
  - consistency
  - distill
  - language
  - domain
  - strategy
  - redteam
  - test
  - architecture
  - debug
done_check:
  - next_task_is_named
  - write_path_is_clear
  - required_context_is_listed
---

# Route Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/analyst.md}}

## Mode Rules

- `route` is always chat-only.
- Start with `Interpreted goal`, then recommend the next task sequence, selected lenses, write path, and Add Context files.
- Do not load templates, create files, or apply lenses automatically.
- If no lens is named by the user, use `Lens: none`.

## When To Use

- Use when the user wants guidance, the request is mixed, the selected task seems wrong, or the next write path is unclear.
- Use when the safest output is a segmented path rather than immediate execution.

## Do Not Use When

- Do not use to perform analysis, planning, review, save, sync, or implementation itself.
- Do not use to apply lenses; only recommend them.

## Expected Output

- Always chat-only.
- Return interpreted goal, boundary, recommended path, mode, lens, target when needed, Add Context, next prompt, and stop points for composite work.

## Task Boundary Check

`route` handles both active guidance and correction routing.

- Use `Boundary Assessment` when the user has selected a task but the request appears composite, wrong-task, or missing prerequisites.
- Output `Recommended Segments` for composite requests.
- Do not silently switch tasks or execute later segments.
- No implicit preflight runs in `route`. Do not scan evidence; only recommend which later task should run implicit preflight.

Boundary classes:

- `fits`: the selected task can handle the request.
- `fits_with_preflight`: the selected task can handle it after read-only preflight in `Mode: discuss`.
- `composite`: multiple tasks are needed.
- `wrong_task`: another task is the proper entrypoint.
- `missing_prerequisite`: target, approved plan, source of truth, or Formal Docs Rules safety is missing.

## Routing Model

Recommend the smallest path:

- Typical session state flow: `discuss loop -> save draft -> review -> save accepted -> build/external-agent -> review -> sync`.
- New background or staged requirements: `clarify` or `explore` -> `save` to `.session/inbox/**`.
- Target direction, option, architecture, or concept: `shape` -> `save` to `.session/drafts/**` or `.session/accepted/**`.
- Repo-aware implementation sequence or handoff: `plan` -> `save` to `.session/drafts/**` or `.session/accepted/**`.
- Session artifact write: `save` -> `.session/**`.
- Native external-agent implementation: external-agent path -> `review` plan audit -> native Implement -> `review` diff.
- Formal documentation alignment: `sync` -> `docs/**` or `src/**/README.md`.
- Code or repository change through workflow: `build` with `Mode: execute` and an approved plan.

## Lens Suggestions

- Suggest `strategy` for route comparison or technical direction.
- Suggest `architecture` for boundaries, dependency direction, public surfaces, or constraints.
- Suggest `domain` for terminology, rules, ownership, and conceptual model questions.
- Suggest `iteration` for multi-turn work with changing background.
- Suggest `redteam` when a draft will become `.session/accepted/**`, a plan will enter `build` or external-agent Implement, the work touches architecture boundaries, permissions, security, data migration, irreversible changes, formal docs, high-cost tradeoffs, or the user asks to find problems, critique, identify pitfalls, or judge reasonableness.
- Do not enable `redteam` automatically; output it only as a suggested lens unless the user explicitly selected it.
- Suggest `test` for verification planning.
- Suggest `debug` for defects or uncertain runtime behavior.
- Suggest `consistency` for code/docs/design drift.
- Suggest `language` for terminology or output language.
- Suggest `distill` for learning structure from strong reference material.
- Suggest `expand` when a compact decision or plan needs examples, pseudocode, or split parts.

## Output Format

```text
Interpreted goal: <one sentence>
Boundary: <fits|fits_with_preflight|composite|wrong_task|missing_prerequisite>
Recommended path: <task -> task>
Mode: <discuss|persist|execute>
Write Path: <workflow-managed|external-agent>
Lens: <none or explicit lenses>
Target: <only when writing>
Add Context:
- .workflow/tasks/<task>.md
- .workflow/lenses/<lens>.md only when selected
- .workflow/templates/<template>.md only for `save` or `sync` in Mode: persist
- .session/goal/*, relevant .session/inbox/**, relevant .session/drafts/**, relevant .session/accepted/**, docs/**, or source files as needed
Next prompt: <copyable prompt>
```

For composite requests:

```text
Recommended Segments:
1. <segment name>
   Mode:
   Task:
   Lens:
   Target/Plan:
   Context:
   Request:
   Expected Output:
   Continue Condition:
Stop Points:
- <approval, audit, source-of-truth, Formal Docs Rules, or diff review point>
```

## User Input

{{goal, situation, or request for workflow guidance}}
