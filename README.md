# Workflow Lite

Workflow Lite is an embeddable prompt workspace for AI-assisted development.

```text
.workflow/   # workflow system: tasks, lenses, roles, templates, guidance
.session/    # AI session working memory: goal, inbox, drafts, accepted, archive
docs/        # formal project documentation, when the host project uses it
.opencode/   # optional OpenCode adapter agents
```

The workflow system belongs in `.workflow/`. Session artifacts belong in `.session/`. Formal long-term project documentation belongs in `docs/**`. Code-adjacent reading notes may live in `src/**/README.md` when useful.

Use `save` for high-fidelity session artifacts from `Save Packet`, `sync` for formal docs / code-adjacent README, and `build` for approved repository changes.

Exploration repos may use explicit `notes/**` targets for disposable exploration notes. Formal project docs remain `docs/**`.

Read [.workflow/README.md](.workflow/README.md) for the full workflow guide.
Read [.workflow/codex.md](.workflow/codex.md) when using Codex manually.
Read [.workflow/opencode.md](.workflow/opencode.md) when using OpenCode.

## Embedding Rule

Do not put workflow system files under `.session/` or `docs/`. Do not treat `.session/**` as formal source of truth. Stable project knowledge, architecture constraints, terminology, and design docs belong in the host project's `docs/**`.

OpenCode support is an adapter, not a second workflow. Keep task, lens, role, and template semantics in `.workflow/**`; use `.opencode/agents/**` only for OpenCode-specific context helper, review, and docs sync behavior.
