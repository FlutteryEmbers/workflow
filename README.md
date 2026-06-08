# Workflow Lite

Workflow Lite is an embeddable prompt workspace for AI-assisted development.

```text
.workflow/   # workflow system: tasks, lenses, roles, templates, guidance
.session/    # AI session working memory: goal, inbox, threads, archive
docs/        # code-aligned project docs, when the host project uses it
.opencode/   # optional OpenCode adapter agents and slash commands
```

The workflow system belongs in `.workflow/`. Session artifacts belong in `.session/`. Code-aligned long-term project docs belong in `docs/**`. Code-adjacent reading notes may live in `src/**/README.md` when useful.

Use `persist` for high-fidelity session artifacts from `Persist Packet`, `sync` for code-aligned project docs / code-adjacent README, and `build` for explicit repository changes.

Exploration repos may use explicit `notes/**` targets for disposable exploration notes. Code-aligned project docs remain `docs/**`.

Read [.workflow/README.md](.workflow/README.md) for the full workflow guide.
Read [.workflow/codex.md](.workflow/codex.md) when using Codex manually.
Read [.workflow/opencode.md](.workflow/opencode.md) when using OpenCode.

For Copilot, use dedicated GitHub prompt commands for common work: `/wf-route`, `/wf-shape`, `/wf-plan`, `/wf-review`, `/wf-persist`, and `/wf-sync`. Use `workflow-lite.prompt.md` as fallback/router for mixed or full-protocol requests.

## Embedding Rule

Do not put workflow system files under `.session/` or `docs/`. Do not treat `.session/**` as project source of truth. Stable project knowledge, architecture constraints, terminology, and design docs belong in the host project's `docs/**`.

OpenCode support is an adapter, not a second workflow. Keep task, lens, role, and template semantics in `.workflow/**`; use `.opencode/commands/**` as thin slash-command shortcuts and `.opencode/agents/**` only for OpenCode-specific context helper, review, and docs sync behavior. `/wf-build` is the explicit-plan execution command and should be used only with a concrete `Plan:`.

## Experiment Workflow Direction

If experiment-driven work becomes an established development mode, keep it as a separate workflow instead of adding another branch inside Workflow Lite.

An ideal experiment workflow should focus on matrix-style validation, comparison, and evidence capture:

- Define the question, variables, baselines, and success criteria.
- Generate a small test or dependency matrix.
- Run or record experiment attempts.
- Compare results with explicit evidence.
- Produce conclusions that can feed back into Workflow Lite as `.session/threads/**`, `docs/**`, or an explicit implementation plan.

Reusable lessons from this project:

- Keep the workflow source separate from project artifacts.
- Prefer explicit write paths over implicit file edits.
- Separate disposable notes, thread conclusions, and project docs.
- Treat review as a gate, not as a redesign task.
- Keep lenses optional; do not turn every useful method into a default branch.
- Preserve reasoning that affects future decisions, but do not store full chat transcripts by default.
- Use project docs only for settled conclusions that prevent future code/docs alignment mistakes.

Features worth preserving in a future experiment workflow:

- `discuss -> persist -> review -> execute/sync` as a general control loop.
- High-fidelity artifact persisting, especially for evidence, assumptions, rejected options, and next use.
- Explicit storage boundaries such as disposable notes, session threads, and project docs.
- External-agent plan audit and diff review for weak or context-limited agents.
- Lightweight exploration notes for throwaway trials.

Do not merge a full experiment lifecycle into this repository unless it remains a small helper to the current design/session workflow. If it needs its own tasks, result directories, run logs, or matrix artifacts, it should be a separate repository.
