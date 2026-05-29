---
id: shape
role: designer
purpose: Form a direction, concept, architecture, goal update, or session decision in chat.
inputs:
  - clarified_context
outputs:
  - chat_shape
  - save_packet
user_selectable_lenses:
  - iteration
  - expand
  - distill
  - language
  - domain
  - strategy
  - redteam
  - architecture
done_check:
  - decision_is_named
  - tradeoffs_are_visible
  - target_docs_are_named_when_relevant
---

# Shape Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: discuss` is default and is the only valid mode for this task.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize views in user-provided lens order, then converge.
- Do not load templates and do not write files.
- If the user asks to save or provides a target, return `Save Packet` and route the write to `save`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user needs a direction, concept, architecture shape, goal update, or session decision.
- Use for vision-driven PoC work where the initial request should be reframed into the smallest useful wedge.

## Do Not Use When

- Do not use only to collect raw context; use `clarify` or `explore`.
- Do not use to judge whether existing code/docs/plan are reasonable; use `review`.
- Do not use when the direction is fixed and the user needs executable steps; use `plan`.
- Do not use to write session artifacts; use `save`.
- Do not use to update formal docs directly; use `sync`.

## Expected Output

- `Reframed Goal`, `Narrowest Useful Wedge`, `Success Criteria`, `Rejected Larger Scope`, tradeoffs, and recommended next step.
- `Save Packet` when the current shape should become a draft, accepted decision, or explicit goal update.

## Task Boundary Check

Before shaping, classify the request:

- `fits`: user asks to form a direction, concept, architecture, goal update, or session decision.
- `fits_with_preflight`: user asks to shape based on current code, formal docs, session context, external tools, references, repository fit, architecture entrypoints, implementation entrypoints, or how to start. In `Mode: discuss`, run default implicit preflight first, then shape.
- `composite`: user asks to shape and save; shape first, then route to `save`.
- `wrong_task`: user only asks whether current code/docs are reasonable; recommend `review`.
- `wrong_task`: user has a fixed target and wants implementation steps; recommend `plan`.
- `composite`: user asks to evaluate reasonableness and then design a replacement; recommend `review -> shape`.

Default implicit preflight runs only in `Mode: discuss`. Output `Evidence`, `Unknowns`, `Boundary`, and a lightweight `Risk Check` before the shape. If evidence is insufficient, source of truth is unclear, or the request should be decomposed, stop and output Recommended Segments.

`Risk Check` is built-in safety, not the `redteam` lens. Use it to name risky assumptions, likely failure modes, and whether a follow-up `review --lens redteam` is recommended. Do not load `.workflow/lenses/redteam.md` unless the user explicitly selected it.

## Copilot Add Context

Required:

- #.workflow/tasks/shape.md
- relevant `.session/goal/*`, `.session/inbox/**`, existing decisions, docs, or source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Convert notes, goals, and discussion into a clear session direction or goal update. For vague or broad goals, first reframe the request. Name the smallest useful wedge that can validate the goal, the success criteria that would make it worth continuing, and the larger scope that is intentionally rejected for now.

When a shape is likely to become `.session/accepted/**`, involves costly reversal, or depends on unverified assumptions, include `Suggested Lens: redteam` as a recommendation rather than applying it automatically.

## Save Packet

When useful, end with:

```text
Save Packet:
Artifact: shape | decision | goal
Status: draft | accepted
Intent: exploration | decision | constraint
Depth: detailed
Topic: <topic>
Suggested Target: .session/drafts/shape_<topic>.md or .session/accepted/decision_<topic>.md
Source Context:
- <goal, session artifact, code/docs evidence, or user correction>
Key Points:
- <current recommendation and core boundaries>
Decision-Relevant Facts:
- <facts that materially affect the direction>
Reasoning Trail:
- <initial direction -> revisions -> current direction>
Options Considered:
- <option and short description>
Why This Option:
- <reason the recommendation is preferred>
Why Not Others:
- <rejected option and reason>
Rejected Options:
- <larger scopes or directions intentionally not chosen>
Risks / Unknowns:
- <risky assumptions, missing evidence, or follow-up review needs>
Examples / Pseudocode:
- <example scenario, conceptual flow, or pseudocode if useful>
Validation Approach:
- <how this shape can be tested or falsified>
Next Use:
- <save draft | review | plan | sync>
```

Use `.session/goal/**` only when the user explicitly provides that target.
If the shape is not worth preserving, output `Save Packet: none`.

## User Input

{{context, options, goal update, design direction, or concept to shape}}
