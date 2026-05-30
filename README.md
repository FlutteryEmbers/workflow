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

## Experiment Workflow Direction

If experiment-driven work becomes a stable development mode, keep it as a separate workflow instead of adding another branch inside Workflow Lite.

An ideal experiment workflow should focus on matrix-style validation, comparison, and evidence capture:

- Define the question, variables, baselines, and success criteria.
- Generate a small test or dependency matrix.
- Run or record experiment attempts.
- Compare results with explicit evidence.
- Produce conclusions that can feed back into Workflow Lite as `.session/accepted/**`, `docs/**`, or an approved implementation plan.

Reusable lessons from this project:

- Keep the workflow source separate from project artifacts.
- Prefer explicit write paths over implicit file edits.
- Separate disposable notes, draft conclusions, accepted conclusions, and formal docs.
- Treat review as a gate, not as a redesign task.
- Keep lenses optional; do not turn every useful method into a default branch.
- Preserve reasoning that affects future decisions, but do not store full chat transcripts by default.
- Use formal docs only for stable, reader-facing conclusions.

Features worth preserving in a future experiment workflow:

- `discuss -> save -> review -> execute/sync` as a general control loop.
- High-fidelity artifact saving, especially for evidence, assumptions, rejected options, and next use.
- Explicit status boundaries such as disposable, draft, accepted, and formal.
- External-agent plan audit and diff review for weak or context-limited agents.
- Lightweight exploration notes for throwaway trials.

Do not merge a full experiment lifecycle into this repository unless it remains a small helper to the current design/session workflow. If it needs its own tasks, result directories, run logs, or matrix artifacts, it should be a separate repository.
