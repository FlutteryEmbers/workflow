# Workflow Lite

Workflow Lite is an embeddable prompt workspace for AI-assisted development.

```text
.workflow/   # workflow system: tasks, lenses, roles, templates, guidance
.session/    # AI session working memory: goal, notes, decisions, archive
docs/        # formal project documentation, when the host project uses it
```

The workflow system belongs in `.workflow/`. Session artifacts belong in `.session/`. Formal long-term project documentation belongs in `docs/**`. Code-adjacent reading notes may live in `src/**/README.md` when useful.

Read [.workflow/README.md](.workflow/README.md) for the full workflow guide.

## Embedding Rule

Do not put workflow system files under `.session/` or `docs/`. Do not treat `.session/**` as formal source of truth. Stable project knowledge, architecture constraints, terminology, and design docs belong in the host project's `docs/**`.
