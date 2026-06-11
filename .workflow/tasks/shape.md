---
id: shape
role: designer
purpose: Default synthesis entry for ambiguous, what-if, strategy, conceptual, direction-setting, goal update, or session decision work in chat.
inputs:
  - clarified_context
outputs:
  - chat_shape
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - iteration
  - expand
  - distill
  - language
  - domain
  - strategy
  - conceptual
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

- Start with `User Intent` unless the request is trivial; it must restate what the user wants, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize views in user-provided lens order, then converge.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user needs a direction, concept, architecture shape, goal update, or session decision.
- Use for vision-driven PoC work where the initial request should be reframed into the smallest useful wedge.
- Use by default when the request is ambiguous, what-if, strategy-oriented, conceptual, direction-setting, or unclear between `shape`, `explore`, and `review`.

## Do Not Use When

- Do not use only to collect raw context; use `clarify` or `explore`.
- Do not use to judge whether existing code/docs/plan are reasonable; use `review`.
- Do not use when the direction is fixed and the user needs executable steps; use `plan`.
- Do not use to approve a plan, code change, diff, project docs update, or existing artifact; use `review`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs directly; use `sync`.

## Expected Output

- `Reframed Goal`, `Narrowest Useful Wedge`, `Success Criteria`, `Rejected Larger Scope`, tradeoffs, and recommended next step.
- `Impact Surface` with scope size, affected surfaces, reversal cost, execution risk, confirmation point, and recommended next abstraction level.
- `Locked Decisions`, `Assumed Decisions`, and `Blocking Decisions` when the shape may feed later planning.
- `Compatibility / Constraint Check` with compatibility pressure, breaking option availability, constraint tension, suggested policy, and whether a human decision is needed.
- `Output: compact` default: short recommendation, risks, and optional `Persist Candidate`.
- `Full Persist Packet` only when the shape should be persisted now or `Output: full` is requested.
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

If missing evidence could change the recommendation, still provide a provisional shape by default. Label it as provisional, name the assumptions, and state `What Would Change My Mind`. Stop and output `Recommended Segments: explore -> shape` only when the missing evidence would affect execution, project docs, source-of-truth judgment, irreversible choices, security, permissions, data migration, or another high-impact decision. If the user actually needs a verdict, stop and recommend `review`. If the target is fixed and the user needs steps, recommend `plan`.

`Risk Check` is built-in safety, not the `redteam` lens. Use it to name risky assumptions, likely failure modes, and whether a follow-up `review --lens redteam` is recommended. Do not load `.workflow/lenses/redteam.md` unless the user explicitly selected it.

Lens use must not change task responsibility. `strategy`, `conceptual`, `domain`, `architecture`, `iteration`, `expand`, `distill`, and `language` may help synthesize direction, but `shape` must not become evidence-only `explore`, verdict-only `review`, or executable `plan`.

## Discussion Freedom

In `Mode: discuss`, the user remains responsible for final judgment. `shape` should provide useful thinking material instead of over-blocking.

- You may output `Provisional Recommendation`, `Candidate Options`, `Best Guess`, and `What Would Change My Mind`.
- Include `Confidence`, `Assumptions`, and `Human Decision Needed` when the recommendation is uncertain or consequential.
- Do not present provisional recommendations as approval, readiness, source of truth, or permission to execute.
- Keep strict write and execution boundaries unchanged; discussion freedom does not allow file writes, project docs sync, or implementation.

When the user explicitly selects `conceptual`, output `Abstraction Level: concept`. Keep the result at concept level: goal, principles, boundaries, key tradeoffs, success criteria, non-goals, impact surface, and validation direction. Do not produce ordered implementation steps, target files, allowed changes, or step-level verification from `shape`.

## Shape / Plan Boundary

`shape` owns concept design. It decides what the direction is, what it is not, and why. It may recommend the next abstraction level, but it does not create a phase plan or implementation plan.

Use decision states when a shape may feed `plan`:

- `Locked Decisions`: user-confirmed or explicit-source decisions that later planning may rely on.
- `Assumed Decisions`: recommended defaults that can support advisory planning; include risk if wrong.
- `Blocking Decisions`: unresolved choices that prevent a build-ready implementation plan.

Use `Impact Surface` to guide later `plan` auto-selection:

```text
Impact Surface
- Scope Size: small | medium | large
- Affected Surfaces: workflow core | task docs | templates | adapters | project docs | source code | tests | other
- Reversal Cost: low | medium | high
- Execution Risk: low | medium | high
- User Confirmation Needed Before: none | phase-plan | implementation-plan | build
- Recommended Next Abstraction Level: phase-plan | implementation-plan
```

Recommend `implementation-plan` only when the scope is small, reversal cost and execution risk are low, and no blocking decision affects source of truth, artifact boundaries, compatibility, architecture constraints, project-docs truth, permissions, data, or security. Otherwise recommend `phase-plan`.

## Compatibility / Constraint Policy

Default policy:

- `Compatibility: preserve`
- `Constraint Mode: respect`

`shape` may automatically identify compatibility pressure and constraint tension, but it must not switch to `Compatibility: breaking` or `Constraint Mode: propose_override | prototype_exception` unless the user explicitly asks for it.

Explicit user triggers:

- Use `Compatibility: breaking` only when the user says `不背兼容`, `breaking`, `no migration`, `no alias`, `do not preserve compatibility`, or equivalent.
- Use `Constraint Mode: propose_override` only when the user asks to challenge, override, or replace current architecture constraints.
- Use `Constraint Mode: prototype_exception` only when the user says this is a temporary PoC, exploration exception, or "先跑通" style exception.

When relevant, output:

```text
Compatibility / Constraint Check
- Compatibility: preserve | breaking
- Constraint Mode: respect | propose_override | prototype_exception
- Compatibility Pressure: low | medium | high
- Breaking Option Available: yes/no
- Constraint Tension: none | mild | strong
- Suggested Policy: preserve | consider breaking | consider override | prototype exception
- Human Decision Needed: yes/no
```

If `Compatibility: breaking` or `Constraint Mode != respect` is explicitly requested, label it as user-requested. If it is only suggested, keep the active policy as `preserve` and `respect`.

## Copilot Add Context

Required:

- #.workflow/tasks/shape.md
- relevant `.session/goal/*`, `.session/inbox/**`, existing decisions, docs, or source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Convert notes, goals, what-if prompts, strategy questions, and discussion into a clear session direction or goal update. For vague or broad goals, first reframe the request. Name the smallest useful wedge that can validate the goal, the success criteria that would make it worth continuing, and the larger scope that is intentionally rejected for now.

You may provide a hypothesis-based recommendation when evidence is incomplete. Label it as provisional, name what `explore` or `review` would need to confirm, and include what would change the recommendation. Do not output approval, acceptance, or implementation readiness verdicts; route those to `review`.

Do not let `shape` become a plan. It may describe a conceptual structure and validation direction, but must not output ordered implementation steps, target files, allowed changes, do-not-touch areas, or step-level verification.

When a shape is likely to drive execution, involves costly reversal, or depends on unverified assumptions, include `Suggested Lens: redteam` as a recommendation rather than applying it automatically.

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants shaped>
Current Read: <optional one line about relevant code/docs/discussion facts>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Provisional Recommendation: <best guess or none>
Impact Surface:
- Scope Size: <small|medium|large>
- Affected Surfaces: <workflow core|task docs|templates|adapters|project docs|source code|tests|other>
- Reversal Cost: <low|medium|high>
- Execution Risk: <low|medium|high>
- User Confirmation Needed Before: <none|phase-plan|implementation-plan|build>
- Recommended Next Abstraction Level: <phase-plan|implementation-plan>
Human Decision Needed: <yes/no and why>
Persist Candidate: Artifact=shape; Artifact ID=shape_<topic>; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/shape_<topic>.md
```

Use `Persist Candidate: none` when the shape is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to整理, refine, or prepare the discussion for persist without writing files:

```text
User Intent: <one line about what the user wants shaped>
Current Read: <optional one line about relevant code/docs/discussion facts>
Refined Direction:
- <current recommendation, abstraction level, concept structure, boundaries, impact surface, and validation direction>
What Would Change My Mind:
- <evidence, constraint, user decision, or risk that would change the recommendation>
Discussion Notes To Preserve:
- <phase boundary, constraint, user correction, example, counterexample, accepted risk, or weak-model handoff detail>
Open Questions:
- <question that could change the shape>
Persist Candidate:
- Artifact=shape; Artifact ID=shape_<topic>; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/shape_<topic>.md
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs a handoff artifact:

```text
Persist Packet:
Artifact: shape | decision | goal
Artifact ID: shape_<topic>
Abstraction Level: <concept | none>
Artifact State: working | settled | superseded
Intent: exploration | decision | constraint
Depth: detailed
Thread: <thread>
Topic: <topic>
Suggested Target: .session/threads/<thread>/shape_<topic>.md or .session/threads/<thread>/decision_<topic>.md
Decision Snapshot:
- <current recommendation, core boundary, narrowest wedge, success criteria, key risk, next use>
Discussion Notes To Preserve:
- <discussion detail worth preserving because it affects understanding, revision, implementation, or audit>
Source Context:
- <goal, session artifact, code/docs evidence, or user correction>
Key Points:
- <current recommendation and core boundaries>
Decision-Relevant Facts:
- <facts that materially affect the direction>
Locked Decisions:
- <confirmed decision and source>
Assumed Decisions:
- <recommended default, why it is acceptable for advisory planning, risk if wrong>
Blocking Decisions:
- <unresolved choice that prevents implementation-plan, or none>
Impact Surface:
- Scope Size: <small|medium|large>
- Affected Surfaces: <workflow core|task docs|templates|adapters|project docs|source code|tests|other>
- Reversal Cost: <low|medium|high>
- Execution Risk: <low|medium|high>
- User Confirmation Needed Before: <none|phase-plan|implementation-plan|build>
- Recommended Next Abstraction Level: <phase-plan|implementation-plan>
Concept Structure:
- <concept> -> <purpose, scope, constraints, validation, notes>
Decision Trail:
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
Compatibility:
- <preserve | breaking; note if explicitly requested>
Constraint Mode:
- <respect | propose_override | prototype_exception; note if explicitly requested>
Compatibility / Constraint Notes:
- <pressure, breaking option, constraint tension, human decision needed>
Operating Constraints:
- <scope, boundary, do-not-assume, compatibility, constraint mode, human decision needed>
Preserve From Discussion:
- <phase boundaries, phase constraints, important examples/counterexamples, user corrections, accepted risks, or weak-model handoff details to preserve>
Examples / Pseudocode:
- <example scenario, conceptual flow, or pseudocode if useful>
Validation Approach:
- <how this shape can be tested or falsified>
Next Use:
- <persist | review | plan | sync>
```

`Artifact ID` is a lightweight reference anchor for later `persist` requests. It is not a file path and does not change artifact kind or directory rules.

Use `.session/goal/**` only when the user explicitly provides that target.
If the shape is not worth preserving, output `Persist Candidate: none`.

## User Input

{{context, options, goal update, design direction, or concept to shape}}
