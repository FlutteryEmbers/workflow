# Workflow Lite Embed

This repository contains an embeddable Workflow Lite setup for use inside a formal project repository.

## Directory Contract

```text
.workflow/
  README.md
  copilot.md
  tasks/
  lenses/
  roles/
  templates/

.docs/
  work/
  changes/
  current/
  knowledge/
  shared/

.github/
  copilot-instructions.md
  prompts/
    workflow-lite.prompt.md
```

## What Goes Where

- `.workflow/` contains the workflow system: tasks, lenses, roles, templates, and usage guidance.
- `.docs/` contains project artifacts produced by the workflow.
- `.github/` contains only thin Copilot entrypoints.
- `src/**/README.md` is an optional code-adjacent reading entrypoint when a code area needs one.

## Usage

- Read `.workflow/README.md` for the full Workflow Lite guide.
- Use `.workflow/copilot.md` as the Copilot Add Context menu.
- Use `.github/prompts/workflow-lite.prompt.md` as the manual VS Code prompt entry.
- Start with `.workflow/tasks/route.md` when you want chat-only usage guidance.
- Default to `Mode: discuss`; use `Mode: persist` to write docs and `Mode: execute` to apply an approved plan.

## Embedding Rule

Keep workflow instructions in `.workflow/` and project documentation in `.docs/`. Do not put workflow system files under `.docs/`, and do not put project artifacts under `.workflow/`.
