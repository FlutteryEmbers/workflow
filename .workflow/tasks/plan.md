---
id: plan
role: planner
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
  - ponytail
done_check:
  - input_sufficiency_classified
  - sequence_is_coherent_when_plan_body_exists
  - constraints_are_named
  - minimum_viable_verification_is_defined_or_input_gap_is_named
---

# Plan Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/planner.md}}

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
- Use when source input must be organized into target outcome, scope, constraints, minimum viable verification, fallback verification, and stop conditions before any write.
- Use after `shape`, `review`, `explore`, or a user-provided direction when the input is enough to organize work.

## Do Not Use When

- Do not use to invent the target direction; use `shape`.
- Do not use to ask ordinary clarification, discovery, target-selection, design, implementation, or authorization questions. The only interactive exception is the compatibility-specific `Compatibility Intake` defined below, after repo preflight proves that user-owned compatibility input is materially required.
- Do not use to judge whether a plan, target, code, or diff is good; use `review`.
- Do not use to identify formal blocking gaps, severity, readiness verdicts, or gate status; use `review`.
- Do not use to implement the plan; use `build` with explicit user invocation and an explicit plan, or use the external-agent path.
- Do not use to write session artifacts; use `persist`.
- Do not use to update stable documents; use `sync`.

## Boundary Layers

- `Core Responsibility`: classify whether the source input can support a plan and, when it can, organize the chosen direction into a coherent, realistically executable plan draft or handoff without performing the work.
- `Adjacent Allowance`: include bounded repo-fit preflight, input gaps, compatibility intake, compatibility/constraint plan, stop conditions, and recommended next task.
- `Forbidden Authority`: do not choose the core direction, ask questions outside the compatibility-specific intake, issue a review verdict, label formal blocking gaps, write files, stable-sync documents, execute, implement, or imply execution authorization.

Adjacent allowance must stay planning-owned. If the primary need is direction choice, formal gap severity, verdict, stable projection, or implementation, route to `shape`, `review`, `sync`, or `build`/external-agent.

## Expected Output

- `Input Sufficiency`: `insufficient | sufficient-for-draft | sufficient-for-handoff`.
- `Input Gaps` only when sufficiency is `insufficient`.
- `Planning Continuation` when sufficiency is `insufficient`.
- `Compatibility Intake` only when its trigger conditions pass and user-owned compatibility input remains unresolved after preflight.
- A draft or handoff plan body only when sufficiency is `sufficient-for-draft` or `sufficient-for-handoff`.
- `Compatibility / Constraint Plan` when compatibility or constraint policy affects execution.
- `Output: compact` default: user intent, input sufficiency, optional input gaps, shape summary, impact surface, plan at a glance, plan body when allowed, compact verification, next step, and optional `Persist Candidate`.
- `Output: full` / `Full Persist Packet` only when the plan should be persisted now, used as a handoff, needs explicit handoff detail, or `Output: full` is requested.

## Task Boundary Check

Before planning, classify the request:

- `fits`: target direction is chosen and the user needs repo-aware sequencing, work packages, or handoff.
- `fits_with_preflight`: plan depends on current code, project docs, session context, target-to-repo fit, target files, or verification entrypoints. In `Mode: discuss`, repo-fit preflight is required before planning.
- `composite`: user asks to plan and persist; plan first, then route to `persist`.
- `wrong_task`: target direction is not chosen; recommend `shape`.
- `wrong_task`: user asks whether current implementation, target, or plan is reasonable; recommend `review`.
- `composite`: user asks to implement from target docs and current code without a concrete plan; recommend `review -> plan -> review -> external-agent/build -> review`.

Repo-fit preflight runs only in `Mode: discuss` and is mandatory when the plan depends on repo facts. It checks target stability, target files or areas, existing patterns, constraints, existing verification entrypoints, compatibility surfaces, repository-local consumers, data/config/file-format lifetime evidence, and whether the selected direction fits current repo reality. Plan may name input gaps and conflicts, but must not perform discovery inventory, decide source of truth, judge missing capability, invent a new target, or issue a formal review verdict. If missing evidence would affect scope, sequence, target files, or any credible minimum verification path, output `Input Sufficiency: insufficient`, output `Input Gaps`, and set `Recommended Next Task: explore`. Do not mark input insufficient solely because old-run baselines, contract snapshots, or broad regression suites are unavailable.

## Copilot Add Context

Required:

- #.workflow/tasks/plan.md
- relevant `.session/inbox/**`, `.session/threads/**`, docs, and target source files

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Write the smallest useful plan for the user's current intent. Do not compare or choose core directions; route that work to `shape`. A plan may be large or staged when the work requires it, but do not classify plans into plan kinds.

`Input Sufficiency` judges the source input, not the model's generated plan quality. It is an advisory planning classifier, not a blocker verdict or authorization boundary:

- `insufficient`: required input is missing and cannot be safely assumed for the requested plan use. Do not output an executable or handoff plan body. Output `Input Gaps` and `Planning Continuation`; recommend `shape`, `explore`, or another `plan` pass, or use `Next: user-answer` when only user-provided input can resolve the gap.
- `sufficient-for-draft`: input is enough to produce a discussion or review draft, but not enough for build or external-agent handoff.
- `sufficient-for-handoff`: input is enough to produce a handoff-grade plan with target outcome, scope, allowed changes, do-not-touch areas, minimum viable verification, fallback verification when needed, residual risk, and stop conditions. This still does not authorize execution.

Use `Input Gaps` only when input is insufficient. List missing input categories, not questions. Valid gap categories include direction, motivation, scope, target, compatibility policy, constraint policy, allowed changes, do-not-touch areas, source of truth, repo evidence, verification, stop conditions, and intended next use. Do not include generic cautions, risk notes, or review concerns as input gaps. Questions belong only to a triggered `Compatibility Intake`; do not turn `Input Gaps` into a generic question list.

Every plan, including compact chat output, must summarize the shaped or chosen direction before planning execution. Use `Shape Summary: Source=chat` when there is no persisted shape artifact. Include `Motivation` as one sentence explaining why the direction matters; use `unknown` when the motivation is not available, and do not invent it. Do not force a shape artifact just to plan.

Every plan, including compact chat output, must include a short `Impact Surface` when a plan body is output. Compact impact surface is a planning reader aid, not a full audit; include only `Scope Size`, `Affected Surfaces`, `Risk`, and `Reversal Cost`.

Persisted plan artifacts include `Impact Surface -> Plan At A Glance` for scanability. `Plan At A Glance` is a surface-level summary of change, target, reason, and risk; it is not a step list, verification plan, full diff, or replacement for the plan body.

Use `Plan` as work packages or sequencing by default. For `sufficient-for-handoff`, the plan may include more explicit execution outline, but it must still be tied to allowed changes, minimum viable verification, fallback verification, residual risk, and stop conditions and must not imply build authorization.

Build handoff wording must use `explicit plan handoff`, not readiness labels. `build` requires explicit user invocation plus plan validation; review is recommended for material risk but is not a universal hard gate. `build` must not infer authorization from `Input Sufficiency`, adjacent discussion output, or any plan label. In `Next` or `Next Use`, write `build with explicit invocation`; keep `Recommended Next Task` as the task id `build`.

`Depth: detailed` is persisted artifact metadata, not a chat output mode. Keep chat output modes to `compact`, `normal`, and `full`.

When `Lens: ponytail` is explicitly selected, encode the Demo Contract into the
existing plan rather than adding a new plan schema. Use scope, constraints,
verification, stop conditions, and notes to name the demo path, controlled
inputs, validation that may be relaxed, fields or abstractions that must not be
added, real-world safety that remains required, deferred work, and observable
triggers for later productionization. The minimum viable verification should
prove the declared demo path, not manufacture production assurance. A handoff
must be explicit enough that build or an external agent does not need to decide
which validation, compatibility, or abstraction work belongs in the PoC.

`ponytail` does not change `Input Sufficiency`, build authorization, or the
compatibility / constraint policy. Do not infer `Compatibility: breaking` or
`Constraint Mode: prototype_exception` from the selected lens.

## Default Verification Policy

Default `plan` optimizes for practical execution. Choose the narrowest existing verification that is target-relevant, low-cost, and executable in the current repo. Prefer, in order:

- existing repo script or manifest command that directly covers the target area;
- existing fixture, unit, static, typecheck, lint, snapshot, or targeted test for the touched module;
- small smoke check, CLI command, fixture replay, prompt/static assertion, or manual acceptance checklist when no automated target exists;
- fallback verification plus explicit residual risk when full verification is unavailable.

Every plan body must include:

```text
Verification
- Minimum Viable Verification: <existing targeted check, static check, smoke, fixture/unit, manual checklist, or none>
- Verification Feasibility: available | partial | unavailable | unknown
- Fallback Verification: <fallback checks when stronger verification is unavailable, or none>
- Residual Risk: <risk left after minimum/fallback verification, or none>
```

Do not require new test infrastructure, old-run baseline capture, complete contract freeze, full regression, or e2e coverage by default. For refactor or migration plans, the absence of an old run or baseline does not by itself make input insufficient. Mark `Input Sufficiency: insufficient` for verification only when there is no credible way to judge whether the plan completed successfully.

Use `Higher Assurance` only when `Lens: test` is selected or the user explicitly requests stronger assurance. Higher assurance may include contract freeze, old baseline replay, parity matrix, full regression, e2e, golden cases, edge cases, rollback validation, or migration validation. In default output, mention these only as optional higher-assurance follow-up, not as prerequisites.

## Discussion Freedom

In `Mode: discuss`, `plan` may output insufficient, draft, or handoff plans.

- `Input Sufficiency` is source-input classification, not a review verdict.
- `sufficient-for-handoff` means the input supports a handoff-grade plan; it is not a review verdict, build verdict, or execution authorization.
- `Input Gaps` name missing input categories only when input is insufficient.
- `Planning Continuation` keeps the discussion useful when input is insufficient. It may summarize known direction, safe partial framing, missing input categories, and the advisory next task, but it must not become an executable plan body or handoff.
- Default verification is minimum viable verification. Use fallback and residual risk when stronger verification is not feasible.
- Do not ask questions from `plan` except a triggered `Compatibility Intake` after repo preflight. The exception is compatibility-only and must not expand into general planning questions.
- Do not output review verdicts, formal blocking gaps, severity, or review-style checklists from `plan`.
- Use `Follow-up Questions` only in normal/full outputs for non-blocking future considerations, and never as a blocking form.

## Compatibility Intake

`Compatibility Intake` is the only interactive Plan exception. It completes
user-owned compatibility input after repo preflight; it does not choose product
direction, source of truth, architecture, implementation technique, target
files, or write/build authorization.

Trigger it only when all conditions are true:

- compatibility policy is not locked by the user or an explicit source;
- preflight finds a real API, CLI, config, persisted-data, or file-format
  compatibility surface;
- `preserve` versus `breaking` materially changes scope, verification,
  migration, fallback, or handoff; and
- repository evidence cannot determine external-consumer obligations, whether
  existing data/config may be discarded, or the acceptable transition window.

Do not trigger it when the user already said `不背兼容`, `breaking`, `no
migration`, or equivalent; no legacy surface exists; the work is purely
additive; preservation cost is negligible; or repo evidence already resolves
the policy.

Before asking, report the evidence basis: compatibility surfaces,
repository-local consumers, data/config/file-format lifetime, preserve cost,
breaking impact, and what remains user-owned. Never ask the user for facts that
read-only repo preflight can discover.

Ask two questions in one intake round by default, with 2-3 mutually exclusive
options each:

1. `Consumer Scope`: controlled demo/repo consumers only; known external
   consumers must continue; or unknown/external exposure requires conservative
   preservation.
2. `Data / Config Lifetime`: disposable and rebuildable; migrate then cut over;
   or old and new formats must work concurrently.

Ask a third `Transition Window` question only when preflight finds an external
consumer, persistent data, or material transition cost. Options are atomic
cutover; temporary compatibility bridge with an explicit removal trigger; or
long-term preservation. When this third question is omitted, preflight has
established that no material transition window exists; treat cutover as atomic
for answer mapping rather than asking an unnecessary third question.

Put the recommended option first and suffix its label with `(Recommended)`.
Explain the scope and verification impact of every option. With explicit
`Lens: ponytail` and evidence that consumers are controlled and data is
disposable, recommend demo-only consumers, rebuildable data, and atomic cutover,
but do not select `breaking` without the user's answer.

Map answers into the existing compatibility policy:

- controlled consumers + disposable data + atomic cutover ->
  `Compatibility: breaking`;
- migrate then cut over -> `Compatibility: breaking`, with the migration and
  old-read/write stop point named;
- temporary bridge -> current `Compatibility: preserve`, with the bridge,
  removal trigger, and later breaking work named;
- unknown consumers, concurrent old/new formats, or long-term preservation ->
  `Compatibility: preserve`.

When native user-input UI is available, ask first and continue planning after
the answers in the same interaction chain. When UI is unavailable, output:

```text
Input Sufficiency: insufficient
Input Gaps:
- compatibility policy
Planning Continuation:
- Known Direction: <chosen direction>
- Cannot Produce Yet: <draft plan | handoff plan | executable plan>
- Advisory Next Task: plan
Compatibility Intake:
- Evidence Found: <surfaces, repo consumers, data lifetime, costs>
- Questions:
  - ID: <consumer-scope | data-config-lifetime | transition-window>
    Prompt: <one compatibility-only question>
    Recommended Option: <option id>
    Options:
      - ID: <stable option id>
        Label: <short label; recommended option includes "(Recommended)">
        Impact: <scope, migration, and verification effect>
- Continue After Answers: rerun plan with the selected compatibility inputs
Next: user-answer
```

While waiting, do not output `Impact Surface`, `Plan At A Glance`, `Plan`,
`Verification`, `Execution Handoff`, `Persist Candidate`, or `Full Persist
Packet`. The unresolved intake is not persisted artifact content. After answers
are available, omit the intake and write the resulting decision into the normal
`Compatibility / Constraint Plan`.

## Compatibility / Constraint Policy

Default policy:

- `Compatibility: preserve`
- `Constraint Mode: respect`

`plan` must encode the selected policy into proposed work packages. It must not silently switch from `preserve` to `breaking`, remove migration/alias/fallback work, or introduce constraint exceptions unless the user explicitly requested them or the source decision already states them.

Use `Compatibility: breaking` only when explicitly requested by the user or explicit source. A user's Compatibility Intake answer is explicit user input. In that case, name removed compatibility, migration/alias decisions, cleanup, and stop conditions.

Use `Constraint Mode: propose_override` or `prototype_exception` only when explicitly requested by the user or explicit source. In that case, name the exception scope, reason, cleanup or review trigger, and whether it must stay out of long-term `docs/**` until confirmed.

If preserving compatibility makes the plan materially more complex and the source input does not choose a policy, run the compatibility preflight and trigger `Compatibility Intake` only when all intake conditions pass. Otherwise use the evidence-backed default policy or report `Input Sufficiency: insufficient` without questions when the missing input belongs to another task.

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
Planning Continuation:
- Known Direction: <known chosen direction, or unknown; only when insufficient>
- Useful Planning Frame Now: <safe partial framing, or none; only when insufficient>
- Cannot Produce Yet: <draft plan | handoff plan | executable plan; only when insufficient>
- Advisory Next Task: <shape|explore|plan|none; only when insufficient>
Compatibility Intake: <only when triggered and native UI is unavailable; omit all plan-body fields and wait for answers>
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
Verification:
- Minimum Viable Verification: <targeted fixture/unit/static/smoke/manual check; omit when insufficient>
- Verification Feasibility: <available|partial|unavailable|unknown; omit when insufficient>
- Fallback Verification: <fallback or none; omit when insufficient>
- Residual Risk: <remaining risk or none; omit when insufficient>
Execution Handoff: <use Output: full or persisted plan for executable handoff; include only when Recommended Next Task is build or external-agent>
Recommended Next Task: <shape|explore|review|plan|persist|sync|build|external-agent|none>
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
Planning Continuation:
- Known Direction: <known chosen direction, or unknown; only when insufficient>
- Useful Planning Frame Now: <safe partial framing, or none; only when insufficient>
- Cannot Produce Yet: <draft plan | handoff plan | executable plan; only when insufficient>
- Advisory Next Task: <shape|explore|plan|none; only when insufficient>
Compatibility Intake: <only when triggered and native UI is unavailable; omit all plan-body fields and wait for answers>
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
- <target outcome, work packages, sequence, constraints, minimum viable verification, fallback verification, and stop conditions; omit when insufficient>
Verification:
- Minimum Viable Verification: <targeted existing checks, static/smoke/manual checks, or none>
- Verification Feasibility: <available|partial|unavailable|unknown>
- Fallback Verification: <fallback checks when stronger verification is unavailable, or none>
- Residual Risk: <remaining risk after minimum/fallback verification, or none>
- Higher Assurance: <only when Lens: test or explicitly requested; otherwise none>
Source Basis:
- <shape summary, evidence/repo basis, assumptions, and open basis>
Compatibility / Constraint Plan:
- <when relevant>
Follow-up Questions:
- <none | non-blocking future consideration>
Recommended Next Task:
- <shape|explore|review|plan|persist|sync|build|external-agent|none>
Next:
- <shape | explore | user-answer | plan | review plan | build with explicit invocation | persist plan | sync | none>
Persist Candidate:
- Artifact=plan; Thread=<thread>; Topic=<topic>; Suggested Target=.session/threads/<thread>/plan_<topic>.md
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, requests `Output: full`, or needs a handoff. Do not output a packet while `Compatibility Intake` is unresolved. This packet is handoff input for `persist`; it is not the final persisted artifact schema. `persist` must load `.workflow/templates/plan.md` and shape the final artifact. A packet becomes build input only when explicitly supplied by the user as the `Plan` for `build` or persisted as a plan and then selected for build:

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
- Planning Continuation: <known direction, safe partial framing, what cannot be produced yet, and advisory next task; only when insufficient>
- Source Basis: <shape summary, evidence/repo basis, assumptions, and unknowns>
- Impact Surface: <scope size, affected surfaces, risk, reversal cost, docs/sync impact, and plan at a glance>
- Plan: <work packages, phases, or sequencing>
- Scope: <allowed changes, do-not-touch areas, and explicit out-of-scope work>
- Verification: <minimum viable verification, feasibility, fallback verification, residual risk, success criteria, checks, and acceptance evidence>
- Stop Conditions: <when to stop instead of expanding scope>
- Compatibility / Constraint Policy: <preserve/breaking and respect/override/exception summary>
- Risk / Recovery: <risks, rollback/recovery, and handoff notes>
Next Use: <persist | review | build with explicit invocation | external-agent | sync | none>
```

If the plan is not worth preserving, output `Persist Candidate: none`.

## User Input

{{decision, target design, repo context, or implementation planning request}}
