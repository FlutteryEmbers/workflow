---
id: plan
role: designer
purpose: Turn a chosen direction and sufficiently specified source input into a repo-aware plan draft or handoff in chat.
inputs:
  - direction_or_target
outputs:
  - chat_plan
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - architecture
  - boundary
  - test
  - language
done_check:
  - input_sufficiency_classified
  - sequence_is_coherent_when_plan_body_exists
  - constraints_are_named
  - verification_is_defined_or_input_gap_is_named
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
- `Mode: execute` is not valid for this task; use `build` with explicit user invocation and a plan validated by `review` or `build`.
- For native Plan/Implement, use the external-agent path.

## When To Use

- Use when the target direction is chosen and the user needs repo-aware sequencing, work packages, or external-agent handoff.
- Use when source input must be organized into target outcome, scope, constraints, verification, and stop conditions before any write.
- Use after `shape`, `review`, `explore`, or a user-provided direction when the input is enough to organize work.

## Do Not Use When

- Do not use to invent the target direction; use `shape`.
- Do not use to ask the user questions; classify missing input and route to `shape`, `explore`, or `user-answer`.
- Do not use to judge whether a plan, target, code, or diff is good; use `review`.
- Do not use to identify formal blocking gaps, severity, readiness verdicts, or gate status; use `review`.
- Do not use to implement the plan; use `build` with explicit user invocation and an explicit plan, or use the external-agent path.
- Do not use to write session artifacts; use `persist`.
- Do not use to update stable documents; use `sync`.

## Boundary Layers

- `Core Responsibility`: classify whether the source input can support a plan and, when it can, organize the chosen direction into a coherent plan draft or handoff without performing the work.
- `Adjacent Allowance`: include bounded repo-fit preflight, input gaps, compatibility/constraint plan, stop conditions, and recommended next task.
- `Forbidden Authority`: do not choose the core direction, ask user questions, issue a review verdict, label formal blocking gaps, write files, stable-sync documents, execute, implement, or imply execution authorization.

Adjacent allowance must stay planning-owned. If the primary need is direction choice, formal gap severity, verdict, stable projection, or implementation, route to `shape`, `review`, `sync`, or `build`/external-agent.

## Expected Output

- `Input Sufficiency`: `insufficient | sufficient-for-draft | sufficient-for-handoff`.
- `Input Gaps` only when sufficiency is `insufficient`.
- A plan body only when sufficiency is `sufficient-for-draft` or `sufficient-for-handoff`.
- `Compatibility / Constraint Plan` when compatibility or constraint policy affects execution.
- `Output: compact` default: user intent, input sufficiency, optional input gaps, shape summary, impact surface, plan at a glance, plan body when allowed, next step, and optional `Persist Candidate`.
- `Output: full` / `Full Persist Packet` only when the plan should be persisted now, used as a handoff, needs explicit handoff detail, or `Output: full` is requested.

## Task Boundary Check

Before planning, classify the request:

- `fits`: target direction is chosen and the user needs repo-aware sequencing, work packages, or handoff.
- `fits_with_preflight`: plan depends on current code, project docs, session context, target-to-repo fit, target files, or verification entrypoints. In `Mode: discuss`, repo-fit preflight is required before planning.
- `composite`: user asks to plan and persist; plan first, then route to `persist`.
- `wrong_task`: target direction is not chosen; recommend `shape`.
- `wrong_task`: user asks whether current implementation, target, or plan is reasonable; recommend `review`.
- `composite`: user asks to implement from target docs and current code without a concrete plan; recommend `review -> plan -> review -> external-agent/build -> review`.

Repo-fit preflight runs only in `Mode: discuss` and is mandatory when the plan depends on repo facts. It checks target stability, target files or areas, existing patterns, constraints, verification entrypoints, and whether the selected direction fits current repo reality. Plan may name input gaps and conflicts, but must not perform discovery inventory, decide source of truth, judge missing capability, invent a new target, or issue a formal review verdict. If missing evidence would affect scope, sequence, target files, or verification, output `Input Sufficiency: insufficient`, output `Input Gaps`, and set `Recommended Next Task: explore`.

## Copilot Add Context

Required:

- #.workflow/tasks/plan.md
- relevant `.session/inbox/**`, `.session/threads/**`, docs, and target source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Write the smallest useful plan for the user's current intent. Do not compare or choose core directions; route that work to `shape`. A plan may be large or staged when the work requires it, but do not classify plans into plan kinds.

`Input Sufficiency` judges the source input, not the model's generated plan quality:

- `insufficient`: required input is missing and cannot be safely assumed. Do not output a plan body. Output `Input Gaps` and recommend `shape`, `explore`, `user-answer`, or another `plan` pass.
- `sufficient-for-draft`: input is enough to produce a discussion or review draft, but not enough for build or external-agent handoff.
- `sufficient-for-handoff`: input is enough to produce a handoff-grade plan with target outcome, scope, allowed changes, do-not-touch areas, verification, and stop conditions. This still does not authorize execution.

Use `Input Gaps` only when input is insufficient. List missing input categories, not questions. Valid gap categories include direction, motivation, scope, target, compatibility policy, constraint policy, allowed changes, do-not-touch areas, source of truth, repo evidence, verification, stop conditions, and intended next use. Do not include generic cautions, risk notes, or review concerns as input gaps.

Every plan, including compact chat output, must summarize the shaped or chosen direction before planning execution. Use `Shape Summary: Source=chat` when there is no persisted shape artifact. Include `Motivation` as one sentence explaining why the direction matters; use `unknown` when the motivation is not available, and do not invent it. Do not force a shape artifact just to plan.

Every plan, including compact chat output, must include a short `Impact Surface` when a plan body is output. Compact impact surface is a planning reader aid, not a full audit; include only `Scope Size`, `Affected Surfaces`, `Risk`, and `Reversal Cost`.

Persisted plan artifacts include `Impact Surface -> Plan At A Glance` for scanability. `Plan At A Glance` is a surface-level summary of change, target, reason, and risk; it is not a step list, verification plan, full diff, or replacement for the plan body.

Use `Plan` as work packages or sequencing by default. For `sufficient-for-handoff`, the plan may include more explicit execution outline, but it must still be tied to allowed changes, verification, and stop conditions and must not imply build authorization.

Build handoff wording must use `explicit plan handoff`, not readiness labels. `build` requires explicit user invocation plus plan validation; review is recommended for material risk but is not a universal hard gate. `build` must not infer authorization from `Input Sufficiency`, adjacent discussion output, or any plan label. In `Next` or `Next Use`, write `build with explicit invocation`; keep `Recommended Next Task` as the task id `build`.

`Depth: detailed` is persisted artifact metadata, not a chat output mode. Keep chat output modes to `compact`, `normal`, and `full`.

## Discussion Freedom

In `Mode: discuss`, `plan` may output insufficient, draft, or handoff plans.

- `Input Sufficiency` is source-input classification, not a review verdict.
- `sufficient-for-handoff` means the input supports a handoff-grade plan; it is not a review verdict, build verdict, or execution authorization.
- `Input Gaps` name missing input categories only when input is insufficient.
- Do not ask questions from `plan`.
- Do not output review verdicts, formal blocking gaps, severity, or review-style checklists from `plan`.
- Use `Follow-up Questions` only in normal/full outputs for non-blocking future considerations, and never as a blocking form.

## Compatibility / Constraint Policy

Default policy:

- `Compatibility: preserve`
- `Constraint Mode: respect`

`plan` must encode the selected policy into proposed work packages. It must not silently switch from `preserve` to `breaking`, remove migration/alias/fallback work, or introduce constraint exceptions unless the user explicitly requested them or the source decision already states them.

Use `Compatibility: breaking` only when explicitly requested by the user or explicit source. In that case, name removed compatibility, migration/alias decisions, cleanup, and stop conditions.

Use `Constraint Mode: propose_override` or `prototype_exception` only when explicitly requested by the user or explicit source. In that case, name the exception scope, reason, cleanup or review trigger, and whether it must stay out of long-term `docs/**` until confirmed.

If preserving compatibility makes the plan materially more complex and the source input does not choose a policy, output `Input Sufficiency: insufficient` with `Input Gaps: compatibility policy`.

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
Input Sufficiency: <insufficient|sufficient-for-draft|sufficient-for-handoff>
Input Gaps:
- <only when insufficient; missing input categories only>
Shape Summary:
- Source: <chat | shape artifact | inbox brief | decision | project docs>
- Motivation: <one sentence or unknown>
- Selected Direction: <one line>
- Key Decisions: <1-3 bullets or none>
- Assumptions: <0-2 bullets or none>
Impact Surface:
- Scope Size: <small | medium | large; omit when insufficient>
- Affected Surfaces: <workflow core | task docs | templates | adapters | project docs | source code | tests | other; omit when insufficient>
- Risk: <low | medium | high; omit when insufficient>
- Reversal Cost: <low | medium | high; omit when insufficient>
Plan At A Glance:
- <1-3 summary changes; target, reason, and risk; omit when insufficient>
Plan:
- <3-6 work packages, phases, or sequencing steps; omit when insufficient>
Recommended Next Task: <shape|explore|user-answer|review|plan|persist|sync|build|external-agent|none>
Next: <shape | explore | user-answer | plan | review plan | build with explicit invocation | persist plan | sync | none>
Persist Candidate: Artifact=plan; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/plan_<topic>.md
```

Use `Persist Candidate: none` when the plan is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to organize, refine, or prepare the plan for persist without writing files:

```text
User Intent: <one line about what the user wants planned>
Current Read: <optional one line about relevant plan/code/docs facts>
Input Sufficiency:
- <insufficient|sufficient-for-draft|sufficient-for-handoff>
Input Gaps:
- <only when insufficient; missing input categories only>
Shape Summary:
- Source: <chat | shape artifact | inbox brief | decision | project docs>
- Motivation: <one sentence or unknown>
- Selected Direction: <chosen direction>
- Key Decisions: <confirmed decisions that affect the plan>
- Assumptions: <recommended defaults and risk if wrong>
Impact Surface:
- Scope Size: <small | medium | large; omit when insufficient>
- Affected Surfaces: <surfaces; omit when insufficient>
- Risk: <low | medium | high; omit when insufficient>
- Reversal Cost: <low | medium | high; omit when insufficient>
Plan At A Glance:
- <3-7 summary changes; target, reason, and risk; omit when insufficient>
Plan:
- <target outcome, work packages, sequence, constraints, and verification approach; omit when insufficient>
Source Basis:
- <shape summary, evidence/repo basis, assumptions, and open basis>
Compatibility / Constraint Plan:
- <when relevant>
Follow-up Questions:
- <none | non-blocking future consideration>
Recommended Next Task:
- <shape|explore|user-answer|review|plan|persist|sync|build|external-agent|none>
Persist Candidate:
- Artifact=plan; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/plan_<topic>.md
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs a handoff. This packet is handoff input for `persist`; it is not the final persisted artifact schema. `persist` must load `.workflow/templates/plan.md` and shape the final artifact. A packet becomes build input only when explicitly supplied by the user as the `Plan` for `build` or persisted as a plan and then selected for build:

```text
Persist Packet:
Artifact: plan
Thread: <thread>
Topic: <topic>
Suggested Target: .session/threads/<thread>/plan_<topic>.md
Source Summary: <source shape/decision, user request, repo context, or project docs basis>
Key Fields:
- Target Outcome: <what should be true after execution>
- Input Sufficiency: <insufficient|sufficient-for-draft|sufficient-for-handoff>
- Input Gaps: <only when insufficient; missing input categories>
- Source Basis: <shape summary, evidence/repo basis, assumptions, and unknowns>
- Impact Surface: <scope size, affected surfaces, risk, reversal cost, docs/sync impact, and plan at a glance>
- Plan: <work packages, phases, or sequencing>
- Scope: <allowed changes, do-not-touch areas, and explicit out-of-scope work>
- Verification: <success criteria, checks, and acceptance evidence>
- Stop Conditions: <when to stop instead of expanding scope>
- Compatibility / Constraint Policy: <preserve/breaking and respect/override/exception summary>
- Risk / Recovery: <risks, rollback/recovery, and handoff notes>
Next Use: <persist | review | build with explicit invocation | external-agent | sync | none>
```

If the plan is not worth preserving, output `Persist Candidate: none`.

## User Input

{{decision, target design, repo context, or implementation planning request}}
