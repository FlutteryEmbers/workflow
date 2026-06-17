---
id: plan
role: designer
purpose: Turn a chosen direction into a repo-aware plan, explicit executable plan candidate, or external-agent handoff in chat.
inputs:
  - decision_or_target
outputs:
  - chat_plan
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - architecture
  - test
  - language
done_check:
  - sequence_is_coherent
  - constraints_are_named
  - verification_is_defined_or_gap_is_named
---

# Plan Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must restate what the user wants planned, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- In `Mode: discuss`, multiple explicit lenses are allowed; organize views in user-provided lens order, then converge.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, requests a handoff, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task; use `build` with explicit user invocation and an executable plan validated by `build`.
- For native Plan/Implement, use the external-agent path.

## When To Use

- Use when the target direction is chosen and the user needs repo-aware sequencing, explicit implementation steps, or external-agent handoff.
- Use when a plan must name success criteria, scope, constraints, verification, and stop conditions before any write.
- Use after `shape`, `review`, `explore`, or user-provided direction when there is enough basis to organize work.

## Do Not Use When

- Do not use to invent the target direction; use `shape`.
- Do not use to judge whether a plan, target, code, or diff is good; use `review`.
- Do not use to identify formal blocking gaps or gate readiness; use `review`.
- Do not use to implement the plan; use `build` with explicit user invocation and an executable plan, or use the external-agent path.
- Do not use to write session artifacts; use `persist`.
- Do not use to update stable documents; use `sync`.

## Boundary Layers

- `Core Responsibility`: organize a chosen direction into a coherent plan or explicit executable plan candidate without performing the work.
- `Adjacent Allowance`: include self-assessed plan readiness, known gaps, review focus, follow-up questions, critique/review recommendation, and recommended next task.
- `Forbidden Authority`: do not choose the core direction, issue a review verdict, label formal blocking gaps, write files, stable-sync documents, execute, implement, or imply execution authorization.

Adjacent allowance must stay planning-owned. If the primary need is direction choice, formal gap severity, verdict, stable projection, or implementation, route to `shape`, `review`, `sync`, or `build`/external-agent.

## Expected Output

- A coherent plan appropriate to the user's requested scope and current evidence.
- `Plan Readiness`: `incomplete | reviewable | execution-candidate`.
- `Known Gaps`: plan-owned missing inputs or weak spots; these are not formal blocking gaps.
- `Review Focus`: what review should inspect before build, sync, or handoff.
- `Compatibility / Constraint Plan` when compatibility or constraint policy affects execution.
- `Output: compact` default: shape summary, impact surface, plan, readiness, known gaps, review focus, next step, and optional `Persist Candidate`.
- `Output: full` / `Full Persist Packet` only when the plan should be persisted now, used as a handoff, needs explicit executable detail, or `Output: full` is requested.

## Task Boundary Check

Before planning, classify the request:

- `fits`: target direction is chosen and the user needs repo-aware sequencing, explicit implementation steps, or handoff.
- `fits_with_preflight`: plan depends on current code, project docs, session context, target-to-repo fit, target files, or verification readiness. In `Mode: discuss`, run default implicit preflight first.
- `composite`: user asks to plan and persist; plan first, then route to `persist`.
- `wrong_task`: target direction is not chosen; recommend `shape`.
- `wrong_task`: user asks whether current implementation, target, or plan is reasonable; recommend `review`.
- `composite`: user asks to implement from target docs and current code without a concrete executable plan; recommend `review -> plan -> optional review -> external-agent/build -> review`.

Default implicit preflight runs only in `Mode: discuss` and checks target stability, repo fit, target areas, constraints, and verification readiness. Plan may name known gaps and conflicts, but must not invent a new target or issue a formal review verdict.

## Copilot Add Context

Required:

- #.workflow/tasks/plan.md
- relevant `.session/inbox/**`, `.session/threads/**`, docs, and target source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Write the smallest useful plan for the user's current intent. Do not compare or choose core directions; route that work to `shape`. A plan may be large or staged when the work requires it, but do not classify plans into plan kinds.

Use `Plan Readiness` as plan self-assessment:

- `incomplete`: the plan is missing key direction, evidence, scope, target, compatibility, source-of-truth, or verification inputs. Recommend `shape`, `explore`, `review`, or another `plan` pass based on `Known Gaps`.
- `reviewable`: the plan is coherent enough for review, but should not be treated as an execution candidate yet. Large staged plans, high-risk plans, and plans with material known gaps usually belong here.
- `execution-candidate`: the plan is explicit enough to be reviewed as possible build/external-agent input. This is not a build verdict; review owns readiness and blocking.

Use `Known Gaps` for plan-owned gaps only. Do not output formal blocking fields or severity from plan. Formal blocking and severity belong to `review`.

Use `Review Focus` to guide review. Examples: scope drift, verification adequacy, target boundaries, compatibility policy, source-of-truth risk, handoff clarity, and stop conditions.

Every plan, including compact chat output, must summarize the shaped or chosen direction before planning execution. Use `Shape Summary: Source=chat` when there is no persisted shape artifact. Do not force a shape artifact just to plan.

Every plan, including compact chat output, must include a short `Impact Surface`. Compact impact surface is a planning reader aid, not a full audit; include only `Scope Size`, `Affected Surfaces`, `Risk`, and `Reversal Cost`.

Build handoff wording must use `explicit executable plan candidate`, not plan kind labels. `build` requires explicit user invocation plus executable plan validation; review is recommended for material risk but is not a universal hard gate. `build` must not infer authorization from `Plan Readiness`.

When recommending `build`, include `Review Recommended: yes | no | strongly`. Use `no` only for low-risk explicit plans. Use `yes` or `strongly` for medium/high risk, breaking changes, constraint overrides, public API, data, security, source-of-truth, stable docs projection, multi-surface work, high reversal cost, or ambiguous verification. For unclear plans, recommend `plan` or `review`, not `build`.

`Depth: detailed` is persisted artifact metadata, not a chat output mode. Keep chat output modes to `compact`, `normal`, and `full`.

## Discussion Freedom

In `Mode: discuss`, `plan` may output a plan that is not ready for execution.

- `Plan Readiness` is a self-assessment, not a gate verdict.
- `Known Gaps` are plan-owned missing inputs or weak spots, not formal blockers.
- `Review Focus` tells review what to inspect.
- Include `Confidence`, `Assumptions`, and `Human Decision State` when the plan depends on incomplete evidence or user-owned choices.
- Do not let any plan imply execution authorization.
- Use `Follow-up Questions` only in normal/full outputs for non-blocking future considerations.

## Compatibility / Constraint Policy

Default policy:

- `Compatibility: preserve`
- `Constraint Mode: respect`

`plan` must encode the selected policy into proposed steps. It must not silently switch from `preserve` to `breaking`, remove migration/alias/fallback work, or introduce constraint exceptions unless the user explicitly requested them or the source decision already states them.

Use `Compatibility: breaking` only when explicitly requested by the user or explicit source. In that case, name removed compatibility, migration/alias decisions, cleanup, and stop conditions.

Use `Constraint Mode: propose_override` or `prototype_exception` only when explicitly requested by the user or explicit source. In that case, name the exception scope, reason, cleanup or review trigger, and whether it must stay out of long-term `docs/**` until confirmed.

If preserving compatibility makes the plan materially more complex, output a known gap or tradeoff instead of switching policy automatically.

Include when relevant:

```text
Compatibility / Constraint Plan
- Compatibility: preserve | breaking
- Constraint Mode: respect | propose_override | prototype_exception
- Removed Compatibility: <old paths, aliases, behavior, schema, prompts, or none>
- Migration / Alias: <kept | removed | none | explicitly not provided>
- Constraint Exceptions: <constraint and reason, or none>
- Do Not Preserve: <legacy behavior intentionally dropped, or none>
- Cleanup Required: <old files, docs, prompts, tests, or none>
- Stop Conditions: <when breaking scope or exceptions exceed the explicit plan>
```

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants planned>
Shape Summary:
- Source: <chat | shape artifact | inbox brief | decision | project docs>
- Selected Direction: <one line>
- Key Decisions: <1-3 bullets or none>
- Assumptions: <0-2 bullets or none>
Impact Surface:
- Scope Size: <small | medium | large>
- Affected Surfaces: <workflow core | task docs | templates | adapters | project docs | source code | tests | other>
- Risk: <low | medium | high>
- Reversal Cost: <low | medium | high>
Plan:
- <3-6 steps, phases, or work packages>
Plan Readiness: <incomplete|reviewable|execution-candidate>
Readiness Rationale: <why this readiness applies>
Known Gaps:
- <none | missing direction, evidence, target, verification, source-of-truth, compatibility, or scope input>
Review Focus:
- <what review should inspect before build, sync, or handoff>
Recommended Next Task: <shape|explore|review|plan|persist|sync|build|external-agent|none>
Review Recommended: <no|yes|strongly>
Next: <review | build with explicit invocation | persist plan | sync | shape | none>
Persist Candidate: Artifact=plan; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/plan_<topic>.md
```

Use `Persist Candidate: none` when the plan is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to organize, refine, or prepare the plan for persist without writing files:

```text
User Intent: <one line about what the user wants planned>
Current Read: <optional one line about relevant plan/code/docs facts>
Shape Summary:
- Source: <chat | shape artifact | inbox brief | decision | project docs>
- Selected Direction: <chosen direction>
- Key Decisions: <confirmed decisions that affect the plan>
- Assumptions: <recommended defaults and risk if wrong>
Impact Surface:
- Scope Size: <small | medium | large>
- Affected Surfaces: <surfaces>
- Risk: <low | medium | high>
- Reversal Cost: <low | medium | high>
Refined Plan:
- <target outcome, sequence, constraints, and verification approach>
Planning Basis:
- <source shape/decision, locked decisions, assumed decisions, rejected options, and known gaps>
Plan Readiness:
- <incomplete|reviewable|execution-candidate>
Readiness Rationale:
- <why this readiness applies>
Known Gaps:
- <none | missing or weak planning input>
Review Focus:
- <what review should inspect>
Follow-up Questions:
- <none | non-blocking future consideration>
Recommended Next Task:
- <shape|explore|review|plan|persist|sync|build|external-agent|none>
Review Recommended:
- <no|yes|strongly>
Persist Candidate:
- Artifact=plan; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/plan_<topic>.md
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs an explicit executable plan candidate / external-agent handoff. A full packet is still chat output; it becomes build input only when explicitly supplied by the user as the `Plan` for `build` or persisted as a plan and then selected for build:

```text
Persist Packet:
Artifact: plan
Artifact State: working | settled | superseded
Intent: handoff | decision | audit
Depth: detailed
Thread: <thread>
Topic: <topic>
Suggested Target: .session/threads/<thread>/plan_<topic>.md
Plan Summary:
- Target Outcome: <what should be true after execution>
- Recommended Path: <short sequence, staged path, or work package summary>
- Plan Readiness: <incomplete|reviewable|execution-candidate>
- Readiness Rationale: <why this readiness applies>
- Next Action: <shape | explore | review | plan | build | external-agent | sync | persist | none>
- Review Recommended: <no | yes | strongly>
- Main Risk: <main risk or none>
- Source Basis: <chat | shape artifact | inbox brief | decision | project docs>
Shape Summary:
- Source: <chat | shape artifact | inbox brief | decision | project docs>
- Selected Direction: <one-line direction>
- Key Decisions: <decisions that shape execution>
- Assumptions: <defaults and risk if wrong>
Impact Surface:
- Scope Size: <small | medium | large>
- Affected Surfaces: <surfaces>
- Risk: <low | medium | high>
- Reversal Cost: <low | medium | high>
- Docs / Sync Impact: <none | suggested | required>
Planning Basis:
- Source Direction: <shape artifact, decision, user request, project doc, or inferred target>
- Locked Decisions: <confirmed decisions and sources>
- Assumed Decisions: <recommended defaults and risk if wrong>
- Rejected Options: <options rejected because they affect sequence, scope, or constraints; none if not relevant>
- Known Gaps: <none | missing or weak planning input>
Source Context:
- <thread decision, target design, code/docs evidence, or planning discussion>
Target Outcome:
- <what should be true after execution>
Plan:
- <steps, phases, or work packages with scope, constraints, verification, and stop conditions>
Allowed Changes:
- <paths, behavior, docs, tests, or prompts allowed to change>
Do Not Touch:
- <areas excluded from the plan>
Compatibility / Constraint Plan:
- Compatibility: <preserve | breaking>
- Constraint Mode: <respect | propose_override | prototype_exception>
- Removed Compatibility: <old path, alias, behavior, schema, prompt, or none>
- Migration / Alias: <kept | removed | none | explicitly not provided>
- Constraint Exceptions: <exception scope and reason, or none>
- Do Not Preserve: <legacy behavior intentionally dropped, or none>
- Cleanup Required: <old files, docs, prompts, tests, or none>
- Stop Conditions: <when to stop and return to plan/review>
Step -> Verify:
- <step> -> <verification>
Review Focus:
- <what review should inspect before build, sync, or handoff>
Risks / Unknowns:
- <execution risk or missing information>
Follow-up Questions:
- <none | non-blocking future consideration>
Preserve From Discussion:
- <important examples/counterexamples, user corrections, accepted risks, or handoff details>
Handoff Notes:
- <minimal context for build or external-agent>
Stable Document Follow-up:
- <none | project-docs | session-archive; include target, sync domain, and reason only when future alignment or retrieval could drift>
Next Use:
- <persist | review | build | external-agent | sync>
Recommended Next Task:
- <shape | explore | review | plan | persist | sync | build | external-agent | none>
Review Recommended:
- <no | yes | strongly>
```

If the plan is not worth preserving, output `Persist Candidate: none`.

## User Input

{{decision, target design, repo context, or implementation planning request}}
