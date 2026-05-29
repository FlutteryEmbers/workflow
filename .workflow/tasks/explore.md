---
id: explore
role: designer
purpose: Understand code, materials, current behavior, feasibility, or reference structure in chat.
inputs:
  - question_or_source
outputs:
  - chat_findings
  - save_packet
user_selectable_lenses:
  - distill
  - strategy
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
- If the user asks to save or provides a target, return `Save Packet` and route the write to `save`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user needs evidence about current code, docs, behavior, feasibility, reference material, or unknowns.
- Use before `shape` or `plan` when the decision depends on repository reality or external references.

## Do Not Use When

- Do not use to make the final direction decision; use `shape`.
- Do not use to judge whether a plan, diff, or implementation is acceptable; use `review`.
- Do not use to create executable steps; use `plan`.
- Do not use to write session artifacts; use `save`.
- Do not use to update formal docs; use `sync`.

## Expected Output

- Facts, evidence, assumptions, unknowns, and suggested next decision.
- `Save Packet` when findings should become an inbox note, option draft, or distillation artifact.

## Task Boundary Check

Before exploring, classify obvious boundary problems:

- `fits`: user asks to understand code, docs, behavior, feasibility, or reference material.
- `fits_with_preflight`: request scope is too broad, source is unclear, or the request may actually belong to `review`, `shape`, or `plan`. In `Mode: discuss`, run conditional boundary preflight only.
- `composite`: user asks to explore and save; explore first, then route to `save`.
- `wrong_task`: user asks to choose a direction; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to judge a target or diff; recommend `review`.
- `wrong_task`: user asks to update formal docs; recommend `sync`.

Conditional implicit preflight for `explore` only checks boundary, source, and scope. Do not duplicate exploration inside preflight; once the boundary is clear, proceed with normal explore or recommend the right task.

If not `fits`, do not write files. Return Boundary, Reason, Recommended Path, and Next Prompt.

## Copilot Add Context

Required:

- #.workflow/tasks/explore.md
- relevant source files, docs, references, or `.session/**` context

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Explore enough to reduce uncertainty for the next decision. Separate observed facts from assumptions. Preserve reference-derived structure in chat until the user chooses `save`.

## Save Packet

When useful, end with:

```text
Save Packet:
Artifact: note | option | distillation
Status: inbox | draft
Intent: exploration | audit | reference
Depth: standard | detailed
Topic: <topic>
Suggested Target: .session/<inbox|drafts>/<artifact>_<topic>.md
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
Risks / Unknowns:
- <unknowns, weak evidence, or follow-up checks>
Examples / Pseudocode:
- <example, code path, or pseudocode if useful>
Next Use:
- <shape | plan | review | save | sync>
```

If the exploration is not worth preserving, output `Save Packet: none`.

## User Input

{{question, source material, code area, or reference to explore}}
