# Workflow Lite

> Lightweight by default. Session memory is separate from formal docs.

## Workflow

```mermaid
graph TD
    R["route"] --> C["clarify"]
    C --> E["explore"]
    E --> S["shape"]
    S --> P["plan"]
    P --> W["save session artifact"]
    P --> B["build or external-agent"]
    B --> V["review"]
    V --> Y["sync formal docs"]

    C -. "input" .-> N[".session/inbox"]
    W -. "drafts" .-> D[".session/drafts"]
    W -. "accepted" .-> A[".session/accepted"]
    S -. "goal" .-> G[".session/goal"]
    Y -. "formal docs" .-> F["docs"]
```

## Core Ideas

- `task`: lightweight action prompt in `.workflow/tasks/`.
- `role`: small perspective file in `.workflow/roles/`.
- `lens`: optional user-selected thinking method in `.workflow/lenses/`.
- `.session`: AI session working memory, not formal source of truth.
- `.session/goal`: evolving goal space and target docs map.
- `.session/inbox`: unprocessed or lightly structured inputs, background, exploration notes, and reference material.
- `.session/drafts`: work-in-progress shapes, options, plans, and reviews; not approved for execution by default.
- `.session/accepted`: accepted session-level conclusions such as decisions, approved plans, and accepted review verdicts.
- `save`: the only task that writes session artifacts.
- `docs`: formal project documentation maintained by the host project.
- `src/**/README.md`: optional code-adjacent reading entrypoint.

## Session Structure

```text
.session/
  goal/
    vision.md
    target_docs.md
    assumptions.md
    roadmap.md
  inbox/
  drafts/
  accepted/
  archive/
```

Use `.session/**` for work that may change during AI collaboration. Stable long-term knowledge, terms, architecture constraints, and design docs belong in `docs/**`.

## Session Naming

Session directories express state; file prefixes express artifact kind.

- `inbox`: `note_{topic}.md`, `brief_{topic}.md`
- `drafts`: `brief_`, `shape_`, `option_`, `plan_`, `review_`
- `accepted`: `brief_`, `decision_`, `plan_`, `review_`

Do not use task names as mandatory file prefixes. `docs/**` follows the host project's formal docs naming. `src/**/README.md` is fixed.

Drafts are not approved execution sources. `build` and external-agent implementation should prefer `.session/accepted/**`; using `.session/drafts/**` requires explicit user approval and should usually pass through `review`.

## Tasks

| Task | Role | Default Output | Purpose |
| :--- | :--- | :--- | :--- |
| `route` | `analyst` | chat | Recommend the smallest useful next path. |
| `clarify` | `analyst` | chat | Capture staged requirements, background, scope, and acceptance notes. |
| `explore` | `designer` | chat | Understand code, materials, behavior, feasibility, or reference structure. |
| `shape` | `designer` | chat | Form a direction, concept, architecture, goal update, or session decision. |
| `plan` | `designer` | chat | Turn a chosen direction into a repo-aware plan or external-agent handoff. |
| `save` | `steward` | `.session/**` | Save distilled session artifacts from discussion, drafts, or user-provided sources. |
| `build` | `builder` | repository changes | Apply an approved workflow-managed plan. |
| `review` | `reviewer` | chat | Review behavior, evidence, plans, diffs, decisions, or docs alignment. |
| `sync` | `steward` | `docs/**` or `src/**/README.md` | Align formal docs and code-adjacent README files with confirmed decisions, code, or diffs. |

## Lenses

Lenses are user-selected. Copilot may suggest a lens, but must not apply it unless the user explicitly names it or adds its file as context.

| Lens | Use When |
| :--- | :--- |
| `iteration` | Multi-turn discussion needs session state, goal changes, decisions, and open questions. |
| `expand` | A decision or plan needs examples, pseudocode, smaller diagrams, or split parts. |
| `consistency` | Session decisions, formal docs, code, tests, or README files may conflict. |
| `distill` | A strong reference document should be studied for reusable structure and writing principles. |
| `language` | Full English, translation, terminology consistency, or formal glossary updates are needed. |
| `domain` | Terms, rules, ownership, boundaries, or conceptual model are unclear. |
| `strategy` | Technical routes or design options need comparison. |
| `redteam` | The current recommendation needs deliberate critique. |
| `test` | Behavior needs stronger verification. |
| `architecture` | Structure, interfaces, dependencies, constraints, or durable tradeoffs matter. |
| `debug` | A defect or uncertain behavior needs diagnosis. |

## Mode And Write Boundaries

- `Mode: discuss`: chat only; no templates and no writes.
- `Task: save` in `Mode: persist`: writes session artifacts to `.session/**`.
- `Task: sync` in `Mode: persist`: writes only `docs/**` or explicit `src/**/README.md`.
- `Mode: execute`: uses `Task: build` with an approved plan.
- External-agent path: native Plan -> Implement from Codex, Copilot, OpenCode, or similar agents, with plan audit before implementation and diff review afterward.

`Mode: execute` is workflow-managed execution only.

Native Plan/Implement is a separate external-agent write path.

## Task Boundary Router

Before acting, classify whether the request fits the selected task:

- `fits`: the task can handle it directly.
- `fits_with_preflight`: the task can handle it after a read-only preflight.
- `composite`: multiple tasks are needed.
- `wrong_task`: another task is the proper entrypoint.
- `missing_prerequisite`: required target, approved plan, source of truth, or formal docs safety is missing.

Composite requests should return segmented prompts with stop points. Do not silently switch tasks or automatically run later write/implementation segments.

## Save-Centered Session Writes

Discussion tasks do not write files. They should end with `Suggested Save` when the current output is worth preserving.

- `save` writes `.session/**`.
- `save` can infer targets for `.session/inbox/**` and `.session/drafts/**`.
- `.session/accepted/**` requires explicit accepted, approved, or promote intent.
- Explicit `.session/**` targets are respected even when the file name does not follow the recommended prefix.
- Targets outside `.session/**` are routed instead of rejected: `docs/**` and `src/**/README.md` go to `sync`; code, `.workflow/**`, and `.github/**` go to `build` or external-agent.

Saved artifacts preserve decision trail, not full transcript. Include discussion content only when it explains why the artifact exists, why the recommendation changed, why options were rejected, or what remains unresolved.

## Implicit Preflight

Implicit preflight is a same-response, read-only check that can run automatically in `Mode: discuss`. It does not require the user to ask for preflight explicitly.

- Default implicit preflight: `shape`, `plan`, `sync`.
- Conditional implicit preflight: `review`, `build`, `explore`.
- No implicit preflight: `clarify`, `route`.

Implicit preflight must not load templates, write files, run implementation, run tests, perform sync, apply unselected lenses, or do a full repository scan. In `Mode: persist` and `Mode: execute`, do not run implicit preflight; block when prerequisites are missing.

## Built-in Safety Checks

Built-in safety checks are core protocol, not a lens. They do not load `.workflow/lenses/redteam.md` and do not use the full `critique.md` template.

- `shape`: name key unknowns, risky assumptions, and whether a separate `review --lens redteam` is recommended.
- `plan`: include `Step / Change / Verify / Risk / Stop Condition` for major steps.
- `build`: stop on unapproved scope expansion instead of editing beyond the approved plan.
- `sync`: use Formal Docs Rules and output `docs blocked` when formal-doc safety is unclear.
- `review`: recommend `redteam` when the target is costly, ambiguous, or about to enter execution.

Safety checks should stay lightweight and should not block ordinary PoC discussion unless a write, execution, formal docs update, source-of-truth decision, or irreversible change is at risk.

## Prompt Discipline

These rules are core protocol, not optional lenses:

- `Success Criteria First`: before `plan`, `build`, or `sync` writes, name what must be true when the work is done.
- `Step -> Verify`: every major plan step needs a matching verification method.
- `Minimal Diff`: implementation changes only the approved scope; no drive-by refactors, formatting churn, or opportunistic cleanup.
- `Stop On Scope Expansion`: if execution reveals that scope must expand, stop and return to `plan` or `review`.
- `Readiness Before Write`: external-agent plans and diffs should be reviewed before further implementation or formal docs sync.

This project borrows prompt discipline from agent prompt repositories, but it does not copy role-command systems and does not add a root Claude-specific instruction file by default.

## Formal Docs Rules

Any write to `docs/**` must:

- Name source, target audience, and source of truth.
- Name reader-facing success criteria for the target document.
- Preserve stable, confirmed facts useful to formal readers.
- Preserve existing docs structure and tone when updating an existing file.
- Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, internal redteam-only risks, temporary workarounds, sensitive implementation detail, and not-yet-announced plans.
- Output `docs blocked` and do not write `docs/**` when source, audience, source of truth, reader-facing success criteria, or safety is unclear.

## Common Paths

- Usage guidance: `route`.
- Stage requirements or background: `clarify -> save -> .session/inbox/**`.
- Explore code or reference material: `explore -> save -> .session/inbox/**` or `.session/drafts/option_*.md`.
- Shape a direction or goal: `shape -> save -> .session/drafts/**` or `.session/accepted/**`.
- Plan work or handoff: `plan -> save -> .session/drafts/**` or `.session/accepted/**`.
- Multi-turn design: `shape/review/explore discuss loop -> save draft -> review -> save accepted`.
- Native implementation: external-agent native Plan -> `review` audit -> native Implement -> `review` diff.
- Workflow-managed implementation: `plan -> build`.
- Formal docs sync: `sync -> docs/**`.
- Code-adjacent README sync: `sync -> src/**/README.md`.

## Using With Copilot

- Add one task file from `.workflow/tasks/`.
- Add selected lenses only when explicitly named.
- Add templates only for `save` or `sync` in `Mode: persist`.
- Add relevant `.session/goal/*`, `.session/inbox/**`, `.session/drafts/**`, `.session/accepted/**`, `docs/**`, and source files.
- Use `.workflow/copilot.md` as the Add Context menu.

## Using With OpenCode

- Use `.workflow/opencode.md` as the OpenCode adapter guide.
- Keep `.workflow/**` as the source of truth; do not create a separate OpenCode workflow.
- Use OpenCode first as a read-only context helper when its context management or model quality is uncertain.
- Treat OpenCode native Plan output as an external draft plan, not as approved work.
- Audit OpenCode plans with `review` before implementation and review diffs afterward.
- OpenCode bounded implementation should execute only approved narrow segments.
- Temporary `.opencode/plans/` files are scratch; use `save` to persist draft handoffs to `.session/drafts/**` and accepted handoffs to `.session/accepted/**`.

## Using With Codex

- Codex support is manual. This project does not add `AGENTS.md` by default.
- Add or read `.workflow/codex.md` when you want Codex to follow Workflow Lite.
- Do not load all `.workflow/**`; use one task, explicitly selected lenses, and relevant context.
- Template files are persist-only and should be loaded only for `save` or `sync` in `Mode: persist`.
- Codex native Plan -> Implement is `Write Path: external-agent`, not `Mode: execute`.
- If a future project needs automatic agent instructions, add a shared `AGENTS.md` separately; this repository does not require it.

## Default Language

Workflow artifacts default to Chinese explanations with English technical terms preserved. Preserve code identifiers, file paths, API names, package names, CLI commands, front matter keys, and schema keys in English. Use full English only when explicitly requested.

## Rules

- Keep the default path light.
- Default to `Mode: discuss`.
- Start non-trivial responses with an inline `Understanding Check`.
- Select lenses only when the user explicitly asks or adds them as context.
- Multiple lenses are allowed in `Mode: discuss` only when explicitly listed.
- Do not create formal docs from session material without source, audience, and source of truth.
- Use Mermaid diagrams only when they reduce understanding cost.
- Workflow Root is the repository root containing `.workflow/`.
