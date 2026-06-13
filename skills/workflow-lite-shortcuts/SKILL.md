---
name: workflow-lite-shortcuts
description: "Route Workflow Lite short task phrases to repo-local task files. Use when the user says `wf route`, `wf clarify`, `wf explore`, `wf shape`, `wf plan`, `wf review`, `wf persist`, `wf build`, `wf sync`, `workflow <task>`, `use workflow task <task>`, or asks for a Workflow Lite task shortcut in a repo that contains `.workflow/tasks/**`."
---

# Workflow Lite Shortcuts

Use this skill as a thin shortcut layer over a repo-local Workflow Lite installation. Do not copy protocol rules into the skill response. `.workflow/**` remains the source of truth.

## Shortcut Map

- `wf route` -> read `.workflow/tasks/route.md`
- `wf clarify` -> read `.workflow/tasks/clarify.md`
- `wf explore` -> read `.workflow/tasks/explore.md`
- `wf shape` -> read `.workflow/tasks/shape.md`
- `wf plan` -> read `.workflow/tasks/plan.md`
- `wf review` -> read `.workflow/tasks/review.md`
- `wf persist` -> read `.workflow/tasks/persist.md`
- `wf build` -> read `.workflow/tasks/build.md`
- `wf sync` -> read `.workflow/tasks/sync.md`

Also accept `workflow <task>` and `use workflow task <task>` for the same task names.

## Use Rules

1. Load only the mapped task file for the requested shortcut.
2. Load `.workflow/README.md` only when task boundaries, write boundaries, mode rules, or routing are unclear.
3. Do not load every task, lens, template, adapter, prompt, or session artifact by default.
4. Load lens files only when the user explicitly names a lens.
5. Load templates only when the selected task and mode call for them.
6. If the request is mixed or the task name is unclear, use `route` and `.workflow/README.md` instead of guessing multiple tasks.

## Boundaries

- `wf build` still requires an explicit executable plan. The shortcut does not authorize scope expansion, unplanned writes, or execution from `notes/**`.
- `wf persist`, `wf sync`, and `wf build` keep their normal write boundaries. The shortcut does not grant write permission.
- `.workflow/tasks/**` and `.workflow/README.md` override this skill when there is any conflict.
