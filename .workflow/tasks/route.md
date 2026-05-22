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

## Task Boundary Check

`route` handles both active guidance and correction routing.

- Use `Boundary Assessment` when the user has selected a task but the request appears composite, wrong-task, or missing prerequisites.
- Output `Recommended Segments` for composite requests.
- Do not silently switch tasks or execute later segments.

Boundary classes:

- `fits`: the selected task can handle the request.
- `fits_with_preflight`: the selected task can handle it after read-only preflight in `Mode: discuss`.
- `composite`: multiple tasks are needed.
- `wrong_task`: another task is the proper entrypoint.
- `missing_prerequisite`: target, approved plan, source of truth, or Formal Docs Rules safety is missing.

## Routing Model

Recommend the smallest path:

- New background or staged requirements: `clarify` or `explore` -> `.session/notes/**`.
- Target direction, option, architecture, or concept: `shape` -> `.session/decisions/**` or `.session/goal/**`.
- Repo-aware implementation sequence or handoff: `plan` -> `.session/decisions/**`.
- Native external-agent implementation: external-agent path -> `review` plan audit -> native Implement -> `review` diff.
- Formal documentation alignment: `sync` -> `docs/**` or `src/**/README.md`.
- Code or repository change through workflow: `build` with `Mode: execute` and an approved plan.

## Lens Suggestions

- Suggest `strategy` for route comparison or technical direction.
- Suggest `architecture` for boundaries, dependency direction, public surfaces, or constraints.
- Suggest `domain` for terminology, rules, ownership, and conceptual model questions.
- Suggest `iteration` for multi-turn work with changing background.
- Suggest `redteam` for deliberate critique.
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
Mode: <discuss|persist|execute or external-agent path>
Lens: <none or explicit lenses>
Target: <only when writing>
Add Context:
- .workflow/tasks/<task>.md
- .workflow/lenses/<lens>.md only when selected
- .workflow/templates/<template>.md only in Mode: persist
- .session/goal/*, relevant .session/notes/**, relevant .session/decisions/**, docs/**, or source files as needed
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
