# Workflow Lite

> **Philosophy**: lightweight by default, heavier only when needed.
>
> Tasks define actions. Roles define perspective. Lenses add optional depth. Directories describe information state.

## Workflow

```mermaid
graph TD
    R["route"] --> C["clarify"]
    C --> E["explore"]
    E --> S["shape"]
    S --> P["plan"]
    P --> B["build"]
    B --> V["review"]
    V --> Y["sync"]

    C -. "change lens" .-> CH[".docs/changes"]
    E -. "knowledge lens" .-> K[".docs/knowledge"]
    Y -. "durable facts" .-> CUR[".docs/current"]
```

## Core Ideas

- `task`: a lightweight action prompt in `.workflow/tasks/`.
- `role`: a small perspective file in `.workflow/roles/`.
- `lens`: an optional escalation method in `.workflow/lenses/`.
- `.docs`: workflow workspace, not official project documentation.
- `.docs/work`: default place for everyday workflow artifacts, drafts, and reviews.
- `.docs/changes`: optional tracked workspace for larger changes.
- `.docs/current`: workflow-maintained current facts, not published project docs.
- `.docs/knowledge`: raw material and reusable wiki-style explanations.
- `.docs/shared`: project-wide terms, rules, and boundaries used by the workflow.
- `docs`: official project documentation, written only as a sanitized published view.

## Tasks

| Task | Role | Default Output | Purpose |
| :--- | :--- | :--- | :--- |
| `route` | `analyst` | chat | Recommend the smallest useful next path. |
| `clarify` | `analyst` | `.docs/work/briefs/` | Clarify goals, scope, assumptions, and acceptance. |
| `explore` | `designer` | `.docs/work/notes/` | Understand code, material, feasibility, or current behavior. |
| `shape` | `designer` | `.docs/work/shapes/` | Shape a solution, rule, contract, structure, or decision. |
| `plan` | `designer` | `.docs/work/plans/` | Turn a chosen direction or target design into repo-aware executable steps. |
| `build` | `builder` | repository changes | Apply an approved plan to code, docs, prompts, templates, or workflow artifacts. |
| `review` | `reviewer` | `.docs/work/reviews/` | Inspect behavior, risks, evidence, or refactor options. |
| `sync` | `steward` | updated docs | Update living docs, archive durable facts, or organize knowledge. |

## Lenses

Lenses are user-selected. Copilot may suggest a lens, but must not apply it unless the user explicitly names it or adds its file as context.

| Lens | Use When |
| :--- | :--- |
| `iteration` | Multi-turn discussion needs session state, background deltas, decisions, and open questions. |
| `expand` | A short shape or plan needs details, examples, pseudocode, smaller diagrams, or split part files. |
| `consistency` | Docs, code, tests, code-adjacent README files, or design intent may conflict. |
| `publish` | Internal `.docs/**` content should become sanitized official project documentation under `docs/**`. |
| `distill` | A strong reference document or knowledge system should be studied for reusable structure and writing principles. |
| `language` | Full English, translation, terminology consistency, or glossary maintenance is needed. |
| `domain` | Terms, rules, ownership, or boundaries are unclear. |
| `strategy` | Technical routes or design options need comparison. |
| `redteam` | The current recommendation needs deliberate critique. |
| `test` | Behavior needs stronger verification. |
| `architecture` | Structure, interfaces, dependencies, or durable tradeoffs matter. |
| `change` | Work should be tracked under `.docs/changes/{change_id}`. |
| `knowledge` | Raw material should become reusable long-term knowledge. |
| `debug` | A defect or uncertain behavior needs diagnosis. |

Architecture constraints are not global startup rules. Use `shape --lens architecture` to form architecture decisions, `review --lens architecture` to inspect structural risks, and `sync --lens architecture` to record confirmed constraints in `.docs/shared/boundaries.md`.

## Task Schema

Every task front matter uses this schema:

```yaml
id: <task_id>
role: <analyst|designer|builder|reviewer|steward>
purpose: <one sentence>
inputs:
  - <input>
outputs:
  - <output>
user_selectable_lenses:
  - <lens>
done_check:
  - <completion check>
```

Do not add legacy stage, artifact, skill, gate, or auto-applied lens fields.

## Using With Copilot

Use Copilot as a manual context composer:

- Start with `Mode: discuss` unless you explicitly want to write files or execute an approved plan.
- Add one task file from `.workflow/tasks/` as the main context.
- Add the matching template only in `Mode: persist`.
- Add lens files only when the user explicitly selects them.
- If no lens is named, use `Lens: none`.
- Do not add all task, role, template, or lens files.

Suggested starting points:

- Add #.workflow/copilot.md as the context menu.
- Use `.github/copilot-instructions.md` for short repo-wide behavior.
- Use `.github/prompts/workflow-lite.prompt.md` as a manually invoked VS Code prompt file.

Example:

```text
Mode: discuss
Task: plan
Lens: none
Context:
- #.workflow/tasks/plan.md
- #.docs/work/briefs/brief_example.md
Request:
Discuss a lightweight implementation plan. Do not write files.
```

## Getting Usage Guidance

If you are not sure which task or lens to use, start with `route`. It returns a chat-only guide with the recommended task order, lens suggestions, Add Context list, and next prompt.

Example:

```text
Task: route
Lens: none
Context:
- #.workflow/tasks/route.md
Request:
根据我的目标，推荐 task 顺序、lens、Add Context 和下一步提示词：我想先讨论目标架构，再根据当前 repo 拆计划。
```

## Using With Codex

Codex can continue to read task files directly:

- Task files keep `{{CONTENT: /.workflow/roles/...}}` for role injection.
- Template files are persist-only and are not injected by default.
- Lens files are not injected by default.
- Read a lens only when the user explicitly names it or the task input says `Lens: <name>`.
- In `Mode: discuss`, do not write files.
- In `Mode: persist`, write `.docs/**`, except `sync` may target `src/**/README.md` or `docs/**` with `publish`.
- In `Mode: execute`, use `build` with an approved plan before modifying broader repository artifacts.
- Native Codex/Copilot Plan -> Implement is the external-agent write path. It is not `Mode: execute`; use `.workflow` as context, plan constraints, audit checklist, and diff review.

## Conversation-To-Artifact Workflow

Use this workflow when ideas evolve through chat before they become files:

```text
discuss -> persist -> execute
```

- `Mode: discuss`: no writes, no templates, chat-only reasoning with task/lens guidance.
- `Mode: persist`: documentation artifact writes only. Use `.docs/**`, `Task: sync` for `src/**/README.md`, or the `sync + publish` fast path for `docs/**`.
- `Mode: execute`: repository changes through `Task: build` and an approved `Plan`.

## Write Paths

Workflow Lite supports two write paths:

- `workflow-managed`: use `Mode: persist` or `Mode: execute`. Workflow rules control the write boundary.
- `external-agent`: use Codex/Copilot native Plan -> Implement. Workflow files provide context, constraints, audit checklist, and diff review, but they are not the executor.

Use `workflow-managed` when you want controlled persistence, durable `.docs/**` artifacts, or `build` execution from an approved plan. Use `external-agent` when you want Codex/Copilot native Plan/Implement to edit files directly.

External-agent path:

1. Discuss the goal with Workflow Lite, usually with `shape`, `plan`, or `review`.
2. Use native Plan to create an implementation plan only; do not edit files in that phase.
3. Audit the external plan with `review` before implementation.
4. Use native Implement to edit only what the approved external plan lists.
5. Review the resulting diff against the approved plan.

`Mode: execute` is only the workflow-managed execution path through `build`. It is not the only way files may be written.

External-agent publishing:

- For small controlled official docs publishing, prefer `Mode: persist`, `Task: sync`, `Lens: publish`, `Target: docs/**`.
- For multi-file `docs/**` publishing or edits that must preserve existing docs style, native Plan/Implement may be used.
- External publish plans must name `Source`, `Target`, `Audience`, `Source of truth`, publishable facts, do-not-publish content, sanitization steps, blockers, and verification.
- External publish implementation may write only the approved `docs/**` targets.
- After external publish implementation, review the diff with `review --lens publish --lens consistency --lens language`.
- If source, audience, source of truth, or publication safety is unclear, return `Publish blocked`.

Lens behavior:

- Default to `Lens: none`.
- In `Mode: discuss`, multiple lenses are allowed only when explicitly listed, such as `Lens: strategy, domain, architecture`.
- Copilot may recommend lenses, but must not load or apply them automatically.
- In `Mode: persist`, use one primary lens and at most one supporting lens; split into multiple persist steps when more views are needed.
- In `Mode: execute`, prefer execution guardrails like `test`, `debug`, or `change`.

Understanding check:

- Start each non-trivial response with a short inline `Understanding Check`.
- Do not ask for confirmation by default; continue in the same response.
- Ask only when ambiguity would affect file writes, execution, scope, target, plan, or source of truth.

Example:

```text
Mode: discuss
Task: shape
Lens: strategy, domain, architecture
Request:
先讨论 auth refresh 的技术路线，不落库。
```

```text
Mode: persist
Task: shape
Lens: architecture
Target: .docs/work/shapes/shape_auth_refresh.md
Request:
把刚才讨论收敛成目标设计。
```

```text
Mode: persist
Task: plan
Target: .docs/work/plans/plan_auth_refresh.md
Request:
根据 shape 生成 repo-aware implementation plan。
```

```text
Mode: execute
Task: build
Plan: .docs/work/plans/plan_auth_refresh.md
Request:
按计划实现第一阶段。
```

## Default Language

Workflow artifacts default to Chinese explanations with English technical terms preserved.

Preserve task/lens/template names, code identifiers, file paths, API names, package names, CLI commands, front matter keys, and schema keys in English. Use full English only when the user explicitly requests it or selects `language` with an English output request.

Use the `language` lens for full English output, translation, terminology normalization, readability review, or syncing stable terms to `.docs/shared/glossary.md`.

Do not create language-specific directories or filename variants by default.

## `.docs` Structure

`.docs/**` is the Workflow Workspace. It may contain drafts, reasoning, option comparisons, redteam findings, internal risks, and implementation details. It is not official project documentation.

`docs/**` is official project documentation. It must be a sanitized published view for the intended audience.

```text
.docs/
  work/
    briefs/
    notes/
    shapes/
    plans/
    reviews/
    decisions/
  changes/
    {change_id}/
      brief.md
      plan.md
      evidence.md
      archive.md
  current/
    {topic}.md
  knowledge/
    raw/
    wiki/
      index.md
  shared/
    glossary.md
    rules.md
    boundaries.md
```

## Default Paths

- Small request: `clarify -> plan -> build`
- Need context first: `clarify -> explore -> plan -> build`
- Need solution shaping: `clarify -> explore -> shape -> plan -> build`
- Conversation-driven design: `shape --lens iteration --lens strategy -> review --lens redteam -> shape -> sync`
- Target design planning: `shape -> explore -> plan -> review -> plan`
- Expansion: `shape/plan --lens expand -> review --lens redteam -> plan/sync`
- Reference distillation for knowledge: `explore --lens distill --lens knowledge -> sync --lens knowledge`
- Reference distillation for workflow improvement: `explore --lens distill -> shape --lens distill --lens strategy -> plan -> build -> review`
- Bug or risk: `review --lens debug -> plan -> build`
- Consistency review: `review --lens consistency -> sync | shape | plan -> build`
- Official docs publish: `review --lens publish -> sync --lens publish`
- External-agent write: native Plan -> `review` audit -> native Implement -> `review` diff
- External-agent publish: native publish Plan -> `review --lens publish` -> native Implement -> publish diff review
- Larger tracked work: enable `change` lens and use `.docs/changes/{change_id}/`
- Knowledge capture: enable `knowledge` lens and use `.docs/knowledge/`
- Durable fact update: use `sync` and write `.docs/current/{topic}.md`

## Conversation-Driven Workflow

Use this path when the design emerges through multi-turn AI discussion rather than a one-shot spec.

1. Start a session with `shape --lens iteration` and capture the current goal, background added, candidate options, recommendation, rejected options, and open questions.
2. Explore options with `explore --lens strategy` or `shape --lens strategy`; compare 2-4 routes by fit, cost, risk, evidence, and what would change the decision.
3. Add DDD-lite discussion only when useful with `shape --lens domain`: align language, story flow, events, boundaries, rule ownership, and model changes.
4. Challenge the direction with `review --lens redteam`, optionally adding `domain` when critique should inspect terms, events, boundaries, or ownership.
5. Revise with `shape` until the recommendation is stable enough to plan.
6. Sync only stable conclusions with `sync`, writing durable facts to `.docs/current/**`, reusable knowledge to `.docs/knowledge/wiki/**`, and shared language or boundaries to `.docs/shared/**`.

DDD-lite is a discussion technique here, not a default development mode.

## Target Design To Repo Plan

Use this path when you first form a target architecture or conceptual design, then want to plan how the current repository should move toward it.

1. Use `shape` to define the target design. Add `architecture`, `domain`, `strategy`, or `iteration` only when the user selects them.
2. Use `explore` to map that target design onto the current repo: relevant modules, reusable parts, conflicts, impact surface, and unknowns.
3. Use `plan` to produce a repo-aware implementation sequence: target design, current repo fit, impact map, step options, recommended sequence, discussion points, verification, and handoff notes.
4. Use `review --lens redteam` to challenge the plan's step order, hidden costs, boundary conflicts, risk assumptions, and verification gaps.
5. Revise with `plan`, then continue to `build` or use `sync` to preserve only stable conclusions.

This is planning support, not a new architecture workflow. Do not add architecture, conceptual, DDD, TDD, or SDD detail unless the user selects the relevant lens.

## Expansion Outputs

Use `expand` when a compact shape, concept, decision, or plan needs more detail or should be split into smaller drafts.

- Do not create expansion-specific directories.
- Write expansion files next to the source artifact.
- Small expansion: `{base}.expanded.md`.
- Split expansion: `{base}.expanded.md` plus `{base}.part_{topic}.md`.
- The main expanded file must include an `Expansion Index`.
- Each part must declare `Source`, `Part Of`, `Part Topic`, `Depends On`, and `Status`.
- If there are more than 8 parts, keep the same naming scheme and maintain the index instead of creating a new directory.
- Keep expanded content as draft unless `sync` extracts stable conclusions.
- Do not write expanded drafts directly into `.docs/current/**`.

## Distillation

Use `distill` when you find an excellent business document, architecture directory, RFC, ADR, handbook, or knowledge base and want to understand why it works.

Distillation is not ordinary summarization. It should extract observed structure, reader journey, information types, why the document works, transferable structures, non-transferable context, and an adoption recommendation: `adopt`, `adapt`, `reject`, or `revisit`.

Default outputs:

- `explore --lens distill`: `.docs/work/notes/note_{source}_distillation.md`
- `shape --lens distill`: `.docs/work/shapes/shape_distill_{topic}.md`
- `sync --lens distill --lens knowledge`: `.docs/knowledge/wiki/**` or `.docs/shared/**`

Do not let `sync` directly modify `.workflow/**`. If distillation suggests changing `.workflow/templates/**`, `.workflow/lenses/**`, task prompts, or Copilot guidance, first use `shape` to choose the improvement, then `plan` to approve concrete repository changes, then `build` to apply them.

## Code-Adjacent README

Use `src/**/README.md` as an optional local reading entrypoint when a code area has stable responsibilities, important public surface, critical behavior, local decisions, or frequent maintenance cost.

Do not create a code-adjacent README for every directory. Wrapper, barrel, and thin adapter directories usually do not need one.

`sync` is the primary task for maintaining code-adjacent README files after facts are stable. `build` may update them only when an approved plan explicitly requires it.

Use `.workflow/templates/code_readme.md` when maintaining one. The `Scope` field must say exactly which code area the README covers, so it is not confused with a package or project README.

## Consistency Review

Use `review --lens consistency` when docs and code may disagree, or when multiple docs describe the same behavior differently.

Conflict classifications are `doc_stale`, `code_drift`, `docs_conflict`, `ambiguous_intent`, `intentional_exception`, and `low_value`.

Follow-up paths are:

- `sync`: confirmed document drift.
- `shape`: unclear product, design, or architecture intent.
- `plan -> build`: code likely diverged from the intended behavior.
- `none`: accepted exception or low-value difference.

`sync --lens consistency` only updates confirmed document drift. It must not silently rewrite docs to match code when code may be wrong.

## Official Docs Publishing

Use `publish` when stable internal workflow content should become sanitized official project documentation.

Fast path:

```text
Mode: persist
Task: sync
Lens: publish
Target: docs/{area}/{topic}.md
Source:
- .docs/current/{topic}.md
```

This is the only `persist` exception that may write `docs/**`. Ordinary `persist` still writes `.docs/**`, except `sync` may maintain `src/**/README.md`.

Publish rules:

- Preserve stable, confirmed facts useful to the target audience.
- Preserve existing target doc structure and tone when the file already exists.
- Do not copy `.docs/**` directly into `docs/**`.
- Remove AI discussion residue, unresolved tradeoffs, rejected options, redteam-only risks, internal migration steps, code-path detail, temporary workarounds, sensitive implementation detail, security-sensitive detail, and unannounced plans.
- If source, target audience, source of truth, or publication safety is unclear, output `Publish blocked` and do not write `docs/**`.

External-agent publishing is also allowed when native Codex/Copilot Plan/Implement is the chosen write path. It must still follow the same publish rules, audit the native plan before implementation, and review the diff after implementation.

## Rules

- Keep the default path light.
- Default to `Mode: discuss`.
- Start non-trivial responses with an inline `Understanding Check`.
- Templates are persist-only; do not load templates in `Mode: discuss`.
- `Mode: persist` writes `.docs/**`, except `sync` may write `src/**/README.md` or `docs/**` with `publish`.
- `Mode: execute` requires `Task: build` and an approved plan.
- Native Plan/Implement is the external-agent write path, not a Workflow Mode.
- Audit external plans before native implementation and review external diffs afterward.
- Treat `.docs/**` as workflow workspace and `docs/**` as official project documentation.
- Select lenses only when the user explicitly asks or adds them as context.
- Multiple lenses are allowed in `Mode: discuss` only when explicitly listed.
- Copilot may suggest lenses, but must not auto-apply them.
- Explain why a selected lens is being used.
- Do not create change, current, or knowledge artifacts for ordinary small tasks.
- Use Mermaid diagrams only when they reduce understanding cost; do not add diagrams as decoration.
- Use `src/**/README.md` only when it reduces local code reading cost.
- Code remains the source of truth for runtime behavior.
- Workflow Root is the repository root containing `.workflow/`.
