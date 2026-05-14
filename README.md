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

- `task`: a lightweight action prompt in `workflow_core/tasks/`.
- `role`: a small perspective file in `workflow_core/roles/`.
- `lens`: an optional escalation method in `workflow_core/lenses/`.
- `.docs/work`: default place for everyday workflow artifacts.
- `.docs/changes`: optional tracked workspace for larger changes.
- `.docs/current`: durable facts synced from code, changes, or knowledge.
- `.docs/knowledge`: raw material and reusable wiki-style explanations.
- `.docs/shared`: project-wide terms, rules, and boundaries.

## Tasks

| Task | Role | Default Output | Purpose |
| :--- | :--- | :--- | :--- |
| `route` | `analyst` | chat | Recommend the smallest useful next path. |
| `clarify` | `analyst` | `.docs/work/briefs/` | Clarify goals, scope, assumptions, and acceptance. |
| `explore` | `designer` | `.docs/work/notes/` | Understand code, material, feasibility, or current behavior. |
| `shape` | `designer` | `.docs/work/shapes/` | Shape a solution, rule, contract, structure, or decision. |
| `plan` | `designer` | `.docs/work/plans/` | Turn a chosen direction into executable steps. |
| `build` | `builder` | code and `src/**/MODULE.md` | Apply an approved plan safely. |
| `review` | `reviewer` | `.docs/work/reviews/` | Inspect behavior, risks, evidence, or refactor options. |
| `sync` | `steward` | updated docs | Update living docs, archive durable facts, or organize knowledge. |

## Lenses

Lenses are optional. A task should state why a lens is enabled.

| Lens | Use When |
| :--- | :--- |
| `domain` | Terms, rules, ownership, or boundaries are unclear. |
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
optional_lenses:
  - <lens>
done_check:
  - <completion check>
```

Do not add `stage`, `artifact_type`, `skills`, or `gates`.

## `.docs` Structure

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
- Bug or risk: `review --lens debug -> plan -> build`
- Larger tracked work: enable `change` lens and use `.docs/changes/{change_id}/`
- Knowledge capture: enable `knowledge` lens and use `.docs/knowledge/`
- Durable fact update: use `sync` and write `.docs/current/{topic}.md`

## Rules

- Keep the default path light.
- Enable lenses only when the user asks or complexity justifies it.
- Explain why a lens was enabled.
- Do not create change, current, or knowledge artifacts for ordinary small tasks.
- Keep `src/**/MODULE.md` next to code.
- Code remains the source of truth for runtime behavior.
- Workflow Root is the repository root containing `workflow_core/`.
