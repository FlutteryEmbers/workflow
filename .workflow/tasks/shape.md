---
id: shape
role: designer
purpose: Default synthesis entry for ambiguous, what-if, strategy, conceptual, direction-setting, goal update, or session decision work in chat.
inputs:
  - clarified_context
outputs:
  - chat_shape
  - persist_packet
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
- If the user asks to persist or provides a target, return `Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user needs a direction, concept, architecture shape, goal update, or session decision.
- Use for vision-driven PoC work where the initial request should be reframed into the smallest useful wedge.
- Use by default when the request is ambiguous, what-if, strategy-oriented, conceptual, direction-setting, or unclear between `shape`, `explore`, and `review`.

## Do Not Use When

- Do not use only to collect raw context; use `clarify` or `explore`.
- Do not use to judge whether existing code/docs/plan are reasonable; use `review`.
- Do not use when the direction is fixed and the user needs executable steps; use `plan`.
- Do not use to approve a plan, code change, diff, project docs update, or accepted artifact; use `review`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs directly; use `sync`.

## Expected Output

- `Reframed Goal`, `Narrowest Useful Wedge`, `Success Criteria`, `Rejected Larger Scope`, tradeoffs, and recommended next step.
- `Persist Packet` when the current shape should become a draft, accepted decision, or explicit goal update.
- `Triage` when the request may actually need evidence, verdict, or executable planning first.

## Task Boundary Check

Before shaping, classify the request:

- `fits`: user asks to form a direction, concept, architecture, strategy, what-if recommendation, goal update, or session decision.
- `fits_with_preflight`: user asks to shape based on current code, project docs, session context, external tools, references, repository fit, architecture entrypoints, implementation entrypoints, or how to start. In `Mode: discuss`, run default implicit preflight first, then shape.
- `composite`: user asks to shape and persist; shape first, then route to `persist`.
- `wrong_task`: user only asks whether current code/docs are reasonable; recommend `review`.
- `wrong_task`: user has a fixed target and wants implementation steps; recommend `plan`.
- `composite`: user asks to evaluate reasonableness and then design a replacement; recommend `review -> shape`.

Default implicit preflight runs only in `Mode: discuss`. Use it as triage plus evidence check before shaping.

Output this before the shape when the request is not trivial:

```text
Triage: <direction|evidence|verdict|plan>
Known Context:
- <facts already available>
Missing Evidence:
- <facts that could change the recommendation>
Needs Explore?: <yes/no and why>
Needs Review?: <yes/no and why>
Can Shape Now?: <yes/no>
```

If missing evidence could change the recommendation, stop and output `Recommended Segments: explore -> shape`. If the user actually needs a verdict, stop and recommend `review`. If the target is fixed and the user needs steps, recommend `plan`.

`Risk Check` is built-in safety, not the `redteam` lens. Use it to name risky assumptions, likely failure modes, and whether a follow-up `review --lens redteam` is recommended. Do not load `.workflow/lenses/redteam.md` unless the user explicitly selected it.

Lens use must not change task responsibility. `strategy`, `domain`, `architecture`, `iteration`, `expand`, `distill`, and `language` may help synthesize direction, but `shape` must not become evidence-only `explore`, verdict-only `review`, or executable `plan`.

## Copilot Add Context

Required:

- #.workflow/tasks/shape.md
- relevant `.session/goal/*`, `.session/inbox/**`, existing decisions, docs, or source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Convert notes, goals, what-if prompts, strategy questions, and discussion into a clear session direction or goal update. For vague or broad goals, first reframe the request. Name the smallest useful wedge that can validate the goal, the success criteria that would make it worth continuing, and the larger scope that is intentionally rejected for now.

You may provide a hypothesis-based recommendation when evidence is incomplete, but label it as provisional and name what `explore` or `review` would need to confirm. Do not output approval, acceptance, or implementation readiness verdicts; route those to `review`.

When a shape is likely to become `.session/accepted/**`, involves costly reversal, or depends on unverified assumptions, include `Suggested Lens: redteam` as a recommendation rather than applying it automatically.

## Persist Packet

When useful, end with:

```text
Persist Packet:
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
- <persist draft | review | plan | sync>
```

Use `.session/goal/**` only when the user explicitly provides that target.
If the shape is not worth preserving, output `Persist Packet: none`.

## User Input

{{context, options, goal update, design direction, or concept to shape}}
