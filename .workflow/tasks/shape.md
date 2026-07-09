---
id: shape
role: shaper
purpose: Default discussion fallback, concept organizer, and next-task router for ambiguous, what-if, option-comparison, concept-level, direction-setting, or session decision work in chat.
inputs:
  - clarified_context
outputs:
  - chat_shape
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - architecture
  - boundary
  - language
  - expert
done_check:
  - need_for_shape_is_classified
  - direction_is_named_when_needed
  - tradeoffs_are_visible_when_needed
  - target_docs_are_named_when_relevant
---

# Shape Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/shaper.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must restate what the user wants, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize views in user-provided lens order, then converge.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user needs a direction, concept, architecture shape, or session decision.
- Use for vision-driven PoC work where the initial request should be reframed into the smallest useful wedge.
- Use by default when the request is ambiguous, what-if, option-comparison, concept-level, direction-setting, or unclear between `shape`, `explore`, and `review`.
- Use as the small fallback when the user's intent is unclear but not primarily explanation, evidence extraction, specified-source summary, verdict, write, sync, execution, or implementation.
- Use when the user asks how to think about something, what paths exist, or what the next useful move should be.
- Use when a request mixes adjacent discussion needs but has not reached formal `plan`, `review`, `build`, or external-agent readiness.

## Do Not Use When

- Do not use only to collect raw context; use `clarify` or `explore`.
- Do not use to judge whether existing code/docs/plan are reasonable; use `review`.
- Do not use to judge whether something should change or is worth changing; use `review` for change-seeking judgment.
- Do not use when the direction is fixed and the user needs executable steps; use `plan`.
- Do not use to approve a plan, code change, diff, project docs update, or existing artifact; use `review`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs directly; use `sync`.

## Boundary Layers

- `Core Responsibility`: form a concept-level direction, decision frame, option comparison, smallest useful wedge, and recommended next workflow task.
- `Adjacent Allowance`: include lightweight clarification, lightweight compression of current chat or provided snippets, candidate evidence needs, risk sketch, and non-executable planning sketch when they support shaping the direction.
- `Forbidden Authority`: do not perform formal evidence extraction, specified-source summary/distillation, gate verdict, approval, readiness judgment, source-of-truth judgment, explicit executable plan candidate, stable sync, file write, execution, or implementation.

Adjacent allowance must stay secondary to the shape. If the adjacent work becomes the main deliverable, route to the specialized task.

## Expected Output

- `Need For Shape` immediately after `User Intent` and `Current Read`, before `Human Decision State` or recommendation.
- `Reframed Goal`, `Narrowest Useful Wedge`, `Success Criteria`, `Rejected Larger Scope`, tradeoffs, and recommended next step.
- `Impact Surface` with scope size, affected surfaces, reversal cost, execution risk, confirmation point, and recommended next abstraction level.
- `Locked Decisions`, `Assumed Decisions`, and `Open Decisions` when the shape may feed later planning.
- `Compatibility / Constraint Check` with compatibility pressure, breaking option availability, constraint tension, suggested policy, and whether a human decision is needed.
- `Output: compact` default: short recommendation, risks, and optional `Persist Candidate`.
- `Full Persist Packet` only when the shape should be persisted now or `Output: full` is requested.
- `Triage` only when task boundary, evidence readiness, or verdict/planning need is unclear.
- `Boundary Advice`, `Adjacent Allowance Used`, and `Advisory Next Task` when the request uses `shape` as the small fallback.

## Task Boundary Check

Before shaping, classify the request:

- `fits`: user asks to form a direction, concept, architecture, option comparison, what-if recommendation, or session decision.
- `fallback_fit`: primary intent is direction-shaping, but the request includes lightweight adjacent clarification, compression, candidate evidence needs, risk sketch, or planning sketch.
- `fits_with_preflight`: user asks to shape based on current code, project docs, session context, external tools, references, repository fit, architecture entrypoints, implementation entrypoints, or how to start. In `Mode: discuss`, run default implicit preflight first, then shape.
- `composite`: user asks to shape and persist; shape first, then route to `persist`.
- `wrong_task`: user only asks whether current code/docs are reasonable; recommend `review`.
- `wrong_task`: user primarily asks for evidence extraction, source discovery, or how something works; recommend `explore`.
- `wrong_task`: user primarily asks to summarize, distill, compress, or extract structure from a specified source; recommend `distill`.
- `wrong_task`: user has a fixed target and wants implementation steps; recommend `plan`.
- `wrong_task`: user asks to write, stable-sync, execute, implement, or modify repository files; recommend `persist`, `sync`, `plan -> optional review -> build`, or external-agent depending on target and plan readiness.
- `composite`: user asks to evaluate reasonableness and then design a replacement; recommend `review -> shape`.

Default implicit preflight runs only in `Mode: discuss`. Use it as triage plus bounded evidence check for direction shaping.

Shape may inspect current context or do a small bounded evidence check only to decide whether a direction-shaped response can proceed. It must not output a full evidence inventory, discovery map, or source reliability audit. If evidence gathering becomes the main deliverable, route to `explore`.

Output this before the shape when task boundary, evidence readiness, or verdict/planning need is unclear:

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

If missing evidence could change the recommendation, still provide a provisional shape by default when it can be useful and safe. Label it as provisional, name the assumptions, and state `What Would Change This`. Return a boundary-only response only when the request requires writes, stable projection, execution, source-of-truth judgment, irreversible decisions, security, permissions, data migration, or another high-impact decision that cannot be handled as discussion. If the user actually needs a verdict, provide `Shape Continuation` as a review handoff and recommend `review`. If the target is fixed and the user needs steps, provide carry-forward context and recommend `plan`.

Use `fallback_fit` only in `Mode: discuss`. It allows a useful shape when the specialized task would be too heavy for the user's current intent. It does not authorize writes, stable projection, formal verdicts, executable sequencing, or implementation.

`Embedded Critique Check` is core protocol, not the `redteam` lens. Use it to name risky assumptions, likely failure paths, and whether a later explicit redteam critique is worth running. Do not load `.workflow/lenses/redteam.md` from `shape`, and do not output a formal review verdict.

Lens use must not change task responsibility. `architecture`, `boundary`, `language`, and `expert` may help synthesize direction; `boundary` may define ownership, dependency direction, contract/provider/package responsibilities, and provider-owned capability business vs main-system business. `expert` may sharpen assumptions, tradeoffs, and recommendation density, but output must stay at concept level. `shape` must not become evidence-only `explore`, verdict-only `review`, or executable `plan`. Option comparison is built into `shape`; it does not require a separate lens.

## Discussion Freedom

In `Mode: discuss`, the user remains responsible for final judgment. `shape` should provide useful thinking material instead of over-blocking.

- You may output `Provisional Recommendation`, `Candidate Options`, `Best Guess`, and `What Would Change My Mind`.
- When `Boundary Fit: fallback_fit`, you may also output lightweight clarification, lightweight compression, candidate evidence needs, risk sketch, or a non-executable planning sketch.
- Include `Confidence`, `Assumptions`, and `Human Decision State` when the recommendation is uncertain or consequential.
- Do not present provisional recommendations as approval, readiness, source of truth, or permission to execute.
- Keep strict write and execution boundaries unchanged; discussion freedom does not allow file writes, stable-document sync, or implementation.

## Need For Shape

`Need For Shape` is an advisory classifier. It says how this shape response should continue; it does not decide whether the user is allowed to keep discussing the topic. It does not judge whether code, docs, plans, or previous work are correct, fixed, acceptable, ready, or worth changing.

Every non-trivial shape output must include:

```text
Need For Shape:
- Status: needs-direction | already-settled | answerable-now | needs-evidence | needs-review
- Reason: <why this continuation type fits>
- Advisory Next Task: <shape|explore|review|plan|persist|none>
```

Use statuses as follows:

- `needs-direction`: the user still needs a concept direction, option choice, architecture shape, or session decision. Continue normal shape with `Continuation Type: new-direction`.
- `already-settled`: the direction is already fixed in current discussion or explicit source context. Do not re-open options by default; output carry-forward context and recommend `plan`, `persist`, `review`, or `none`.
- `answerable-now`: the user needs a short conceptual answer, not a full new direction. Answer briefly and state the shape implication.
- `needs-evidence`: source or repo facts could change the direction. Provide a provisional continuation or shape inputs needed, then recommend `explore -> shape`.
- `needs-review`: the user is asking whether something is reasonable, solved, ready, correct, worth changing, or should be changed. Provide a review handoff without a review verdict, then recommend `review`.

Every non-trivial shape output must include:

```text
Shape Continuation:
- Continuation Type: new-direction | carry-forward | short-answer | provisional | review-handoff
- Current / Provisional Direction: <direction, short answer, or none>
- Carry Forward: <boundaries, assumptions, or next-use notes>
- What Would Change This: <evidence, review result, or user decision>
- Advisory Next Task: <shape|explore|review|plan|persist|none>
```

When `Status` is not `needs-direction`, avoid generating new candidate options or a new final recommendation unless the user explicitly asks to keep shaping despite the advisory signal. You may still output carry-forward context, a provisional continuation, a short answer, or a handoff. Use `Persist Candidate: Artifact=shape` only when the response materially forms or updates a direction; otherwise use `Persist Candidate: none` or recommend a note.

`Need For Shape` must not use review language or fields. Do not output `ready`, `needs changes`, review verdicts, change assessment fields, blocking gaps, or review rating fields from `shape`.

## Human Decision State / User Checkpoint

`Human Decision State` is discussion guidance and must appear after `Need For Shape` and before a finalized recommendation when a user-owned choice matters. It is not write, sync, or build authorization.

- `none`: no user-owned choice blocks the shape; continue normally.
- `assumed`: a low-risk choice exists; choose the recommended default, continue shaping, and record it under `Assumed Decisions`.
- `checkpoint`: a consequential choice can be expressed as 2-3 real options; output one `User Checkpoint` and wait for selection before finalizing recommendation, impact surface, or `Persist Candidate`.
- `unresolved`: the choice is too risky, underspecified, or evidence-dependent to finalize; continue with provisional direction, assumptions, `What Would Change This`, and the advisory next task.

Use `checkpoint` for choices that affect direction, scope, source of truth, compatibility, constraint policy, artifact boundary, or next planning level. Use at most one checkpoint per shape turn. A checkpoint is allowed only when `Need For Shape Status: needs-direction`.

Checkpoint output:

```text
User Checkpoint
- Question: <choice>
- Why Now: <why the shape cannot safely finalize this without user input>
- Recommended Option: <id>
- Options:
  - ID: <id>
    Label: <label (Recommended) for the first option>
    Explanation: <effect>
    Why Choose This: <fit>
    Risk: <tradeoff>
- Default If Skipped: <recommended default>
- Continue After Selection: continue shape and update Locked/Assumed/Open Decisions
```

For `unresolved`, do not ask a form question. Output the provisional shape and name the user decision, evidence, or review result that would allow the recommendation to be finalized. Provisional discussion never grants write, sync, build, source-of-truth, or irreversible-change authority.

Keep shape output at concept level when it may feed later planning: goal, principles, boundaries, key tradeoffs, success criteria, non-goals, impact surface, and validation direction. Do not produce ordered implementation steps, target files, allowed changes, or step-level verification from `shape`.

## Shape / Plan Boundary

`shape` owns concept design. It decides what the direction is, what it is not, and why. It may recommend the next abstraction level, but it does not create a phase plan or implementation plan.

## External Goal Intake

`shape` is the reasoned projection of external or conversational goal context. It may consume:

- the current chat goal directly
- `.session/inbox/**` brief artifacts with `Brief Type: external-goal`
- relevant `.session/threads/**`, docs, or source evidence

Do not require an inbox brief when the current conversation already contains enough goal context. Use an external-goal brief only when the raw source is long, external, reusable, or worth preserving separately.

Use decision states when a shape may feed `plan`:

- `Locked Decisions`: user-confirmed or explicit-source decisions that later planning may rely on.
- `Assumed Decisions`: recommended defaults that can support advisory planning; include risk if wrong.
- `Open Decisions`: unresolved choices that prevent an explicit executable plan candidate.

Use `Impact Surface` to guide later `plan` auto-selection:

```text
Impact Surface
- Scope Size: small | medium | large
- Affected Surfaces: workflow core | task docs | templates | adapters | project docs | source code | tests | other
- Reversal Cost: low | medium | high
- Execution Risk: low | medium | high
- User Confirmation Needed Before: none | plan | review | build
- Recommended Next Task: plan | review | persist | none
```

Recommend `plan` when the concept direction is selected enough to sequence work. Recommend `review` when the concept needs source-of-truth, readiness, or gap judgment before planning.

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
- Human Decision State: none | assumed | checkpoint | unresolved
```

If `Compatibility: breaking` or `Constraint Mode != respect` is explicitly requested, label it as user-requested. If it is only suggested, keep the active policy as `preserve` and `respect`.

## Copilot Add Context

Required:

- #.workflow/tasks/shape.md
- relevant `.session/inbox/**`, `.session/threads/**`, existing decisions, docs, or source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Convert notes, external-goal briefs, current chat goals, what-if prompts, option questions, and discussion into a clear session direction. For vague or broad goals, first reframe the request. Name the smallest useful wedge that can validate the goal, the success criteria that would make it worth continuing, and the larger scope that is intentionally rejected for now.

You may provide a hypothesis-based recommendation when evidence is incomplete. Label it as provisional, name what `explore` or `review` would need to confirm, and include what would change the recommendation. Do not output approval, acceptance, or implementation readiness verdicts; route those to `review`.

Do not let `shape` become a plan. It may describe a conceptual structure and validation direction, but must not output ordered implementation steps, target files, allowed changes, do-not-touch areas, or step-level verification.

When a shape is likely to drive execution, involves costly reversal, or depends on unverified assumptions, include `Suggested Critique: explicit redteam critique` as a recommendation rather than applying it automatically.

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants shaped>
Current Read: <optional one line about relevant code/docs/discussion facts>
Need For Shape:
- Status: <needs-direction|already-settled|answerable-now|needs-evidence|needs-review>
- Reason: <why this continuation type fits>
- Advisory Next Task: <shape|explore|review|plan|persist|none>
Shape Continuation:
- Continuation Type: <new-direction|carry-forward|short-answer|provisional|review-handoff>
- Current / Provisional Direction: <direction, short answer, or none>
- Carry Forward: <boundaries, assumptions, or next-use notes>
- What Would Change This: <evidence, review result, or user decision>
- Advisory Next Task: <shape|explore|review|plan|persist|none>
Boundary Advice:
- Boundary: <fits|fits_with_preflight|fallback_fit|composite|wrong_task|missing_prerequisite>
- Why: <routing reason or none>
- Useful Response Now: <what shape can still safely provide, or none>
- Advisory Next Task: <task or sequence>
Adjacent Allowance Used: <none|clarification|compression|evidence-needs|risk-sketch|planning-sketch>
Decision State:
- Human Decision State: <none|assumed|checkpoint|unresolved>
- Decision State Reason: <why this state applies>
- Assumed Default: <recommended default or none>
- Checkpoint Needed: <yes/no>
User Checkpoint: <only when state is checkpoint; wait for selection before final recommendation>
Unresolved Decision: <only when state is unresolved; evidence, decision, or review result needed>
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
- User Confirmation Needed Before: <none|plan|review|build>
- Recommended Next Task: <plan|review|persist|none>
Recommended Next Task: <clarify|explore|distill|review|plan|persist|sync|build|external-agent|none>
Persist Candidate: <none unless this response forms or updates a direction; otherwise Artifact=shape; Artifact ID=shape_<topic>; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/shape_<topic>.md>
```

Use `Persist Candidate: none` when the shape is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to organize, refine, or prepare the discussion for persist without writing files:

```text
User Intent: <one line about what the user wants shaped>
Current Read: <optional one line about relevant code/docs/discussion facts>
Need For Shape:
- Status: <needs-direction|already-settled|answerable-now|needs-evidence|needs-review>
- Reason: <why this continuation type fits>
- Advisory Next Task: <shape|explore|review|plan|persist|none>
Shape Continuation:
- Continuation Type: <new-direction|carry-forward|short-answer|provisional|review-handoff>
- Current / Provisional Direction: <direction, short answer, or none>
- Carry Forward: <boundaries, assumptions, or next-use notes>
- What Would Change This: <evidence, review result, or user decision>
- Advisory Next Task: <shape|explore|review|plan|persist|none>
Boundary Advice:
- Boundary: <fits|fits_with_preflight|fallback_fit|composite|wrong_task|missing_prerequisite>
- Why: <routing reason or none>
- Useful Response Now: <what shape can still safely provide, or none>
- Advisory Next Task: <task or sequence>
Adjacent Allowance Used: <none|clarification|compression|evidence-needs|risk-sketch|planning-sketch>
Decision State:
- Human Decision State: <none|assumed|checkpoint|unresolved>
- Decision State Reason: <why this state applies>
- Assumed Default: <recommended default or none>
- Checkpoint Needed: <yes/no>
User Checkpoint: <only when state is checkpoint; wait for selection before refined direction>
Unresolved Decision: <only when state is unresolved; evidence, decision, or review result needed>
Refined Direction:
- <current recommendation, abstraction level, concept structure, boundaries, impact surface, and validation direction>
What Would Change My Mind:
- <evidence, constraint, user decision, or risk that would change the recommendation>
Discussion Notes To Preserve:
- <phase boundary, constraint, user correction, example, counterexample, accepted risk, or weak-model handoff detail>
Open Questions:
- <question that could change the shape>
Recommended Next Task:
- <clarify|explore|distill|review|plan|persist|sync|build|external-agent|none>
Persist Candidate:
- <none unless this response forms or updates a direction; otherwise Artifact=shape; Artifact ID=shape_<topic>; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/shape_<topic>.md>
```

## Full Persist Packet

Output the full packet only when the response forms or updates a direction and the user asks to persist, provides `Target`, or requests `Output: full`. This packet is handoff input for `persist`; it is not the final persisted artifact schema. `persist` must load the matching template and shape the final artifact. Do not output a full persist packet for carry-forward, short-answer, evidence-needed, or review-handoff continuations unless they materially update the direction. Do not output a full persist packet while a `checkpoint` is waiting for selection.

```text
Persist Packet:
Artifact: shape
Artifact ID: shape_<topic>
Thread: <thread>
Topic: <topic>
Suggested Target: .session/threads/<thread>/shape_<topic>.md
Source Summary: <current chat goal, external-goal brief, session artifact, evidence, or user correction>
Key Fields:
- Need For Shape: <status, reason, and advisory next task>
- Shape Continuation: <continuation type, carry-forward notes, and what would change it>
- Recommendation: <current direction, core boundary, and narrowest useful wedge>
- Decision State: <Human Decision State, reason, assumed default, and open decisions>
- Impact Surface: <scope size, affected surfaces, reversal cost, execution risk, and confirmation need>
- Concept Structure: <purpose, scope, constraints, validation, and notes>
- Options / Rejections: <options considered and why the recommendation is preferred>
- Compatibility / Constraint Notes: <preserve/breaking and respect/override/exception summary>
Next Use: <persist | review | plan | sync | none>
```

`Artifact ID` is a lightweight reference anchor for later `persist` requests. It is not a file path and does not change artifact kind or directory rules.

If the shape is not worth preserving, output `Persist Candidate: none`.

## User Input

{{context, options, external goal context, design direction, or concept to shape}}
