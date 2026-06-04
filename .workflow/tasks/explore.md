---
id: explore
role: designer
purpose: Extract evidence about what exists, where it is, how it appears to work, and how reliable the evidence is.
inputs:
  - question_or_source
outputs:
  - chat_findings
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - distill
  - architecture
  - debug
  - language
  - domain
done_check:
  - evidence_is_named
  - unknowns_are_listed
  - next_decision_is_clear
---

# Explore Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default and is the only valid mode for this task.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user clearly needs evidence about current code, docs, behavior, feasibility, reference material, entrypoints, dependencies, or unknowns.
- Use when the user asks what exists, where something is, how something appears to work, whether a capability exists, or how reliable the evidence is.
- Use when the user needs to understand a repository or material before deciding whether to borrow, maintain, review, plan, or redesign anything.
- Use when `shape` cannot safely recommend a direction because missing facts could change the answer.
- Use before `shape` or `plan` when the decision depends on repository reality or external references.

## Do Not Use When

- Do not use to make the final direction decision; use `shape`.
- Do not use for ambiguous what-if, route-comparison, conceptual, or direction-setting requests unless the user primarily asks for evidence.
- Do not use to judge whether a plan, diff, or implementation is acceptable; use `review`.
- Do not use to decide whether docs or code should be modified; use `review` for verdicts and `sync` or `plan/build` only after that verdict.
- Do not use to create executable steps; use `plan`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs; use `sync`.

## Expected Output

- `Sources Checked`, `Observed Facts`, `Evidence Map`, `Reliability Notes`, `Unknowns`, `Constraints Found`, `Potential Options`, and `Recommended Next Task`.
- `Output: compact` default: short findings, key reliability notes, and optional `Persist Hint`.
- `Full Persist Packet` only when findings should be persisted now or `Output: full` is requested.

## Task Boundary Check

Before exploring, classify obvious boundary problems:

- `fits`: user asks to understand code, docs, behavior, feasibility, reference material, entrypoints, dependencies, or source evidence.
- `fits_with_preflight`: request scope is too broad, source is unclear, or the request may actually belong to `review`, `shape`, or `plan`. In `Mode: discuss`, run conditional boundary preflight only.
- `composite`: user asks to explore and persist; explore first, then route to `persist`.
- `wrong_task`: user asks to choose a direction; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to judge a target or diff; recommend `review`.
- `wrong_task`: user asks to update project docs; recommend `sync`.

Conditional implicit preflight for `explore` only checks boundary, source, scope, and evidence type. Do not duplicate exploration inside preflight; once the boundary is clear, proceed with normal evidence extraction or recommend the right task.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Copilot Add Context

Required:

- #.workflow/tasks/explore.md
- relevant source files, docs, references, or `.session/**` context

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Explore enough to reduce uncertainty for the next decision. Separate observed facts from assumptions and inferences. Do not choose the final direction unless the user explicitly routes back to `shape`; prepare evidence for `shape`, `review`, or `plan`.

## Discovery vs Judgment Rule

Do not infer repository ownership or maintenance responsibility. Choose `explore` vs `review` from the user's question type.

- `explore` answers what exists, where it is, how it appears to work, and how reliable the evidence is.
- `review` answers whether something is acceptable, correct, consistent, ready, worth changing, or which source should be treated as truth.
- For code/docs/test/example mismatches during exploration, record `Reliability Notes` instead of repair actions.
- Do not decide which side should be modified.
- Do not recommend `sync` or `build` as the default next task for discovery questions.
- Suggested follow-up should be read-only or interpretive: inspect runtime behavior, check tests, compare versions, ask owner, broaden search, or treat the claim as a hypothesis.
- If the user asks who should change, whether something is acceptable, whether it violates intent, or whether to fix docs/code, route to `review`.

Use this structure for non-trivial output:

- `Sources Checked`: code paths, docs, references, commands, or materials reviewed.
- `Observed Facts`: facts directly supported by checked sources.
- `Evidence Map`: source -> fact -> implication.
- `Reliability Notes`: claims that are contradicted, weak, stale-looking, version-sensitive, or unsafe to rely on.
- `Unknowns`: missing facts or weak evidence.
- `Constraints Found`: boundaries, existing behavior, dependencies, or doc constraints.
- `Potential Options`: candidate materials for `shape`; these are not final recommendations.
- `Recommended Next Task`: usually `shape`, `review`, `plan`, `persist`, or `sync`.

Lens use must not change task responsibility. `distill`, `architecture`, `debug`, `domain`, and `language` may improve evidence extraction, but `explore` must not become final synthesis or verdict.

Use this shape for conflict reliability notes:

```text
Reliability Notes:
- Claim: <what docs, examples, tests, or comments claim>
- Evidence: <code, docs, tests, examples, behavior, or version evidence>
- Conflict: <what does not line up>
- Reliability: <reliable fact | weak signal | hypothesis only | do not rely>
- Possible Explanation: <version drift, stale docs, optional path, incomplete sample, unknown>
- Suggested Follow-up: <inspect runtime behavior | check tests | ask owner | compare versions | treat as hypothesis>
```

## Compact Output By Default

In `Mode: discuss`, default to:

```text
Understanding: <one line>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Persist Hint: Artifact=<note|option|distillation>; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

Use `Persist Hint: none` when the exploration is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to整理, refine, or prepare evidence for persist without writing files:

```text
Understanding: <one line>
Refined Direction / Plan:
- <evidence summary, reliability status, and recommended next task>
Important Context To Preserve:
- <source, reliability note, contradiction, example, or constraint worth preserving>
Open Questions:
- <missing source or evidence gap>
Persist Hint:
- Artifact=<note|option|distillation>; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs a handoff artifact:

```text
Persist Packet:
Artifact: note | option | distillation
Status: inbox | working | stable | superseded
Intent: exploration | audit | reference
Depth: standard | detailed
Thread: <thread or none>
Topic: <topic>
Suggested Target: .session/inbox/<artifact>_<topic>.md or .session/threads/<thread>/<artifact>_<topic>.md
Source Context:
- <code paths, docs, references, or discussion sources checked>
Key Points:
- <main finding or structure discovered>
Decision-Relevant Facts:
- <facts that affect shape, plan, review, or sync>
Reasoning Trail:
- <how evidence led to the current interpretation>
Rejected Options:
- <interpretations or sources ruled out>
Sources Checked:
- <source list>
Observed Facts:
- <source-backed fact>
Evidence Map:
- <source -> fact -> implication>
Reliability Notes:
- <claim, evidence, conflict, reliability, possible explanation, and suggested follow-up>
Constraints Found:
- <constraint or boundary found>
Potential Options:
- <candidate material for shape, not final choice>
Risks / Unknowns:
- <unknowns, weak evidence, or follow-up checks>
Examples / Pseudocode:
- <example, code path, or pseudocode if useful>
Next Use:
- <shape | plan | review | persist | sync>
```

If the exploration is not worth preserving, output `Persist Hint: none`.

## User Input

{{question, source material, code area, or reference to explore}}
