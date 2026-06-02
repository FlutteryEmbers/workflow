# Copilot Instructions

This repo uses Workflow Lite.

- Use one task file as the main workflow context.
- Default to `Mode: discuss`.
- Start with an inline `Understanding Check` unless the request is trivial; do not ask for confirmation unless ambiguity would affect writes, execution, scope, target, plan, or source of truth.
- Before following a task, run a lightweight Task Boundary Check.
- If a request is composite or the selected task is wrong, do not silently switch tasks; return segmented prompts with stop points.
- Read-only preflight is allowed only in `Mode: discuss`; it must not load templates, write files, run implementation, or apply unselected deep lenses.
- Auto write, auto sync, auto build, and auto external-agent implementation are not allowed.
- In `Mode: discuss`, do not load templates and do not create or update files.
- In `Mode: discuss`, multiple lenses are allowed only when explicitly listed; keep `Lens: none` by default.
- Implicit preflight is automatic in discuss for `shape`, `plan`, and `sync`; see `.workflow/copilot.md` for the full matrix.
- In `Mode: persist`, use `Task: persist` for session artifacts under `.session/**`.
- Explicit `notes/**` targets may be persisted as disposable exploration memory; never infer `notes/**`.
- Do not treat `notes/**` as project docs or execution source.
- Discussion tasks do not write files; they should produce `Persist Packet` when useful.
- When persisting session artifacts, preserve decision-relevant reasoning; do not persist full transcript by default.
- `persist` may apply explicit edits, but must not make new shape, plan, or review judgments.
- `Task: sync` in `Mode: persist` may write only `docs/**` or explicit `src/**/README.md` targets.
- `docs/**` is code-aligned project docs and must follow Project Docs Rules.
- Default to `Compatibility: preserve` and `Constraint Mode: respect`; breaking compatibility or constraint exceptions require explicit user intent or an explicit source.
- In `Mode: execute`, require `Task: build` and an explicit executable plan before modifying repository artifacts.
- Native Plan/Implement may be used as an `external-agent` write path; do not treat it as workflow-managed execution.
- External-agent is a write path, not a Mode.
- Audit external plans before native implementation, and review external diffs afterward.
- Use lenses only when the user explicitly names or adds them.
- If no lens is named, assume `Lens: none`.
- Do not load all workflow files or all lenses by default.
- Use `.workflow/copilot.md` as the context menu when guidance is needed.
- Default artifact language is Chinese explanations with English technical terms preserved.
- Use full English only when explicitly requested.
- Use `.session/goal/**` for session goals, `.session/inbox/**` for staged inputs, and `.session/threads/**` for related session artifacts.
- Treat `.session/**` as working memory, not project docs source of truth.
- Use `src/**/README.md` only when a code area needs a local reading entrypoint.
