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
  - conceptual
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

- Do not use to perform analysis, planning, review, persist, sync, or implementation itself.
- Do not use to apply lenses; only recommend them.

## Expected Output

- Always chat-only.
- `Output: compact` default: interpreted goal, recommended path, selected task/lens, and next prompt.
- Full Add Context and stop points only for composite work or `Output: full`.

## Task Boundary Check

`route` handles both active guidance and correction routing.

- Use `Boundary Assessment` when the user has selected a task but the request appears composite, wrong-task, or missing prerequisites.
- Output `Recommended Segments` only for `composite`, `wrong_task`, or `missing_prerequisite`.
- Do not silently switch tasks or execute later segments.
- No implicit preflight runs in `route`. Do not scan evidence; only recommend which later task should run implicit preflight.

Boundary classes:

- `fits`: the selected task can handle the request.
- `fits_with_preflight`: the selected task can handle it after read-only preflight in `Mode: discuss`.
- `composite`: multiple tasks are needed.
- `wrong_task`: another task is the proper entrypoint.
- `missing_prerequisite`: target, explicit plan, source of truth, or Project Docs Rules safety is missing.

## Routing Model

Recommend the smallest path:

- When unsure, start with `shape`. Use `explore` for evidence and `review` for verdict.
- Ambiguous what-if, strategy, conceptual, direction-setting, or entrypoint-selection requests default to `shape`.
- Evidence-only requests such as reading code, finding entrypoints, checking docs, understanding behavior, or studying references go to `explore`.
- Existence and discovery questions such as "does this repo have X", "where is X", "how does X work", or "what evidence exists" go to `explore`.
- Judgment questions such as "is this correct", "is this reasonable", "should this change", "which source is truth", or "is this ready" go to `review`.
- Verdict-only requests such as "is this reasonable", "can this execute", "does this conflict", or "is this ready" go to `review`.
- Typical session flow: `external or conversational goal -> shape/thread artifact -> build/external-agent -> review -> sync`.
- Long or reusable external goal sources: `persist` as `Artifact: brief`, `Brief Type: external-goal`, then `shape` consumes the inbox brief.
- Current conversational goals: `shape` directly, then persist the shaped concept only when worth preserving.
- New background or staged requirements: `clarify` or `explore` -> `persist` to `.session/inbox/**`.
- Target direction, option, architecture, or concept: `shape` -> `persist` to `.session/threads/{thread}/shape_{topic}.md`.
- Repo-aware implementation sequence or handoff: `plan` -> `persist` to `.session/threads/{thread}/plan_{topic}.md`.
- Session artifact write: `persist` -> active `.session/inbox/**` or `.session/threads/**`.
- Active thread metadata or thread file maintenance: `persist` -> `.session/threads/**`.
- Completed thread archive summary: `review/plan -> sync` with `Sync Domain: session-archive` -> `.session/archive/<thread>/summary.md`.
- Native external-agent implementation: external-agent path -> `review` plan audit -> native Implement -> `review` diff.
- Project docs alignment: `review -> plan -> sync` with `Sync Domain: project-docs` -> `docs/**` or `src/**/README.md`.
- Code or repository change through workflow: `build` with `Mode: execute` and an explicit executable plan.
- Discussion chains should end with `Persist Candidate` when the result is worth preserving. `persist` consumes the candidate, recent discussion, or full packet; the original discussion task does not write files.

## Lens Suggestions

- Suggest `strategy` for route comparison or technical direction.
- Suggest `conceptual` for concept-first planning, phase plans, low-level implementation plans, strong-model-to-weak-model handoff, or requests to avoid premature code-level detail.
- Suggest `architecture` for boundaries, dependency direction, public surfaces, or constraints.
- Suggest `domain` for terminology, rules, ownership, and conceptual model questions.
- Suggest `iteration` for multi-turn work with changing background.
- Suggest `redteam` when a thread artifact will drive implementation, a plan will enter `build` or external-agent Implement, the work touches architecture boundaries, permissions, security, data migration, irreversible changes, project docs, high-cost tradeoffs, or the user asks to find problems, critique, identify pitfalls, or judge reasonableness.
- Do not enable `redteam` automatically; output it only as a suggested lens unless the user explicitly selected it.
- Suggest `test` for verification planning.
- Suggest `debug` for defects or uncertain runtime behavior.
- Suggest `consistency` when the user asks for a source-of-truth or maintained-alignment judgment across explicit session sources, project docs, code, tests, code-adjacent README files, workflow artifacts, prompts, templates, or archive summaries.
- Do not suggest `consistency` for discovery questions like whether a capability exists, where it is implemented, or how reliable the evidence is.
- Suggest `language` for terminology or output language.
- Suggest `distill` for learning structure from strong reference material.
- Suggest `expand` when a compact decision or plan needs examples, pseudocode, or split parts.

## Output Format

For normal requests, keep the route compact:

```text
Interpreted goal: <one sentence>
Recommended path: <task -> task>
Lens: <none or explicit lenses>
Next prompt: <copyable prompt>
```

Use the full format only for `Output: full`, composite routing, wrong-task correction, or missing prerequisites:

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
- .workflow/templates/<template>.md only for `persist` or `sync` when persisting
- relevant .session/inbox/**, relevant .session/threads/**, docs/**, or source files as needed
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
- <user decision, audit, source-of-truth, Project Docs Rules, or diff review point>
```

## User Input

{{goal, situation, or request for workflow guidance}}
