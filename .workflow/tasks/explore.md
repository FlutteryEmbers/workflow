---
id: explore
role: explorer
purpose: Structured descriptive inquiry for how something works, what exists, where it appears, observed source-backed differences, entrypoint flow, evidence reliability, and temporary non-mutating probes.
inputs:
  - question_or_source
outputs:
  - chat_findings
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - architecture
  - boundary
  - debug
  - language
done_check:
  - evidence_is_named
  - unknowns_are_listed
  - next_step_is_clear
---

# Explore Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/explorer.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must restate what the user wants, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- Evidence probes are allowed only as non-mutating observation inside `Mode: discuss`; they are not `Mode: execute`.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user clearly needs a descriptive account of current code, docs, behavior, feasibility, reference material, entrypoints, dependencies, or unknowns.
- Use when the user asks what exists, where something is, how something appears to work, what observed differences exist between source-backed paths, whether evidence for a capability exists, or how reliable the evidence is.
- Use when the user needs a non-mutating probe, dry-run, collection command, list command, one-off shell probe, or temporary script outside the repo to establish source-backed evidence.
- Use when the user needs to understand a repository or material before deciding whether to shape a direction or review a claim.
- Use when `shape` cannot safely recommend a direction because missing facts could change the answer.
- Use before `review` when a verdict, gap analysis, or source-of-truth judgment needs evidence first.
- Use before `plan` only when the direction or target is already selected and the remaining need is repo-aware planning context.

## Do Not Use When

- Do not use to make the final direction decision; use `shape`.
- Do not use for ambiguous what-if, route-comparison, concept-level, or direction-setting requests unless the user primarily asks for evidence.
- Do not use to judge whether a plan, diff, or implementation is acceptable; use `review`.
- Do not use to decide whether docs or code should be modified; use `review` for verdicts and `sync` or `plan/build` only after that verdict.
- Do not use to decide whether a system is missing required capability relative to a baseline; use `review` for baseline gap review.
- Do not use to create executable steps; use `plan`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs; use `sync`.
- Do not use to summarize or distill specified source material; use `distill`.

## Boundary Layers

- `Core Responsibility`: answer descriptive inquiry with source-backed observed answers, evidence basis, checked scope, not-found-in-scope reporting, reliability notes, and temporary non-mutating probes.
- `Adjacent Allowance`: include candidate interpretations, likely entrypoints, borrowable ideas, downstream sufficiency, follow-up targets, candidate review targets, and a recommended next task when they help the user use the observed answer.
- `Hard Authority Boundaries`: no durable writes, no stable sync, no implementation or write-path execution, no source-of-truth promotion, and no review verdict or review rating fields.

Explore output must remain descriptive and evidence-backed. If the user asks for judgment, provide observed evidence plus `Candidate Review Targets`; if the user asks for direction, provide observed evidence plus likely `shape` inputs.

## Output Shape

`Output Shape: Observed System Map`

Explore output is shaped around descriptive inquiry:

- `Explore Frame`
- `Observed Answer`
- `Evidence Basis`
- `Evidence Map`
- `Evidence Probes`
- `Reliability / Not Checked`
- `Missing Evidence`
- `Evidence Sufficiency`
- `Downstream Use`
- `Follow-up Targets`
- `Candidate Review Targets`
- `Recommended Next Task`

## Expected Output

- Start with `User Intent`, then `Explore Frame`, then `Observed Answer`; list `Evidence Basis` after answering the user's descriptive question.
- Include `Evidence Probes`, `Reliability / Not Checked`, `Evidence Sufficiency`, `Downstream Use`, `Candidate Review Targets`, `Follow-up Targets`, and `Recommended Next Task` when relevant.
- `Output: compact` default: observed answer, evidence basis, reliability/not-checked notes, downstream use, and optional `Persist Candidate`.
- `Full Persist Packet` only when findings should be persisted now or `Output: full` is requested.

## Task Boundary Check

Before exploring, classify obvious boundary problems. Prefer an in-shape evidence response over `wrong_task` when the user-selected task can still provide useful evidence.

- `fits`: user asks to understand code, docs, behavior, feasibility, reference material, entrypoints, dependencies, source evidence, observed differences, or non-mutating probe results.
- `fits_with_preflight`: request scope is too broad, source is unclear, or the request may actually belong to `review`, `shape`, or `plan`. In `Mode: discuss`, run conditional boundary preflight only.
- `composite`: user asks to explore and persist; explore first, then route to `persist`.
- `fallback_fit`: user asks for a judgment or direction but `explore` can still provide observed-system-map output and candidate next targets.
- `wrong_task`: user asks for writing, stable sync, implementation, or a source-of-truth verdict that cannot be answered with observed-system-map output.

Conditional implicit preflight for `explore` only checks boundary, source, scope, and evidence type. Do not duplicate exploration inside preflight; once the boundary is clear, proceed with normal evidence extraction or recommend the right task.

Boundary handling:

- `fits`: extract evidence in chat.
- `fits_with_preflight`: run the boundary/source/scope/evidence-type preflight, then either extract evidence or route to the right task.
- `fallback_fit`: output observed-system-map material plus `Scope Interpretation`; do not output verdict fields.
- `composite`: explore first, then output the `persist` follow-up prompt; do not write files.
- `wrong_task` or `missing_prerequisite`: stop and return Boundary, Reason, Recommended Path, and Next Prompt.

## Copilot Add Context

Required:

- #.workflow/tasks/explore.md
- relevant source files, docs, references, or `.session/**` context

User-selected lenses:

- Add selected lens files only when the user names them.
- Do not load all lenses by default. If no lens is named, use `Lens: none`.

## Instructions

Explore is structured descriptive inquiry. First answer what is observed in the checked scope, then show the evidence basis. Separate observed facts from assumptions and inferences. You may provide candidate interpretations, likely entrypoints, and borrowable ideas, but do not present them as the final direction, verdict, repair, or baseline gap review; prepare evidence for `shape` or `review` by default. Prepare evidence for `plan` only when the direction or target is already selected and the evidence merely fills repo-aware planning context.

Every non-trivial explore output must start with an `Explore Frame`:

```text
Explore Frame:
- Explore Question: <what the user wants to understand>
- Inquiry Type: how-it-works | what-exists | difference-map | evidence-check | entrypoint-map
- Source Scope: <checked files/docs/commands/materials>
- Downstream Use: shape | review | plan | none
- Stop Rule: <when enough descriptive evidence has been collected>
```

Use `Inquiry Type` to shape the answer:

- `how-it-works`: explain observed behavior, control flow, or document/process mechanics.
- `what-exists`: inventory source-backed items, files, concepts, commands, or surfaces.
- `difference-map`: compare observed source-backed differences without judging which side is correct.
- `evidence-check`: report whether evidence for a claim or capability was found in checked scope.
- `entrypoint-map`: describe entrypoints, call paths, routing, or source flow.

The explanation style is structured current-state description: answer the user's question first, then list evidence. Use "observed", "appears to", "in checked scope", "not found in checked scope", "differs from", and "not checked". Avoid judgment words such as correct, incorrect, should, blocker, ready, gap, must fix, and source of truth.

## Evidence Probe

`explore` may run non-mutating probes and temporary experiments to establish evidence. A probe observes the system; it does not implement, verify completion, or create durable project assets.

Allowed probes:

- Read-only commands such as `rg`, `git grep`, `git diff`, `git show`, `find`, `ls`, or language/tooling commands that only list, collect, parse, or dry-run.
- Existing repo commands used only for non-mutating observation, such as collect/list/dry-run modes.
- One-off shell probes that read files and print derived facts.
- Temporary scripts written outside the repo, such as under `/tmp` or the system temp directory, when a one-liner would be unreliable.

Forbidden probes:

- Creating or modifying repo-tracked scripts, tests, fixtures, configs, generated files, or docs.
- Running migration, formatter, codegen, install, write, delete, publish, deploy, or business-side-effect commands.
- Treating probe success as build verification or implementation completion.
- Concluding that a system is correct, incorrect, missing required capability, blocked, or should be repaired.

Every probe must report:

```text
Evidence Probes:
- Probe: <what fact the probe was meant to establish>
  Command or Method: <command, dry-run, temporary script, or manual inspection>
  Observed Result: <source-backed result>
  Reliability: <reliable fact | weak signal | hypothesis only | do not rely>
  Side Effect Check: <why this did not modify repo/project state>
```

## Discovery vs Judgment Rule

Do not infer repository ownership or maintenance responsibility. Choose `explore` vs `review` from the user's question type.

- `explore` answers what exists, where it is, how it appears to work, and how reliable the evidence is.
- `review` answers whether something is acceptable, correct, consistent, ready, worth changing, or which source should be treated as truth.
- For code/docs/test/example differences during exploration, record `Reliability / Not Checked` instead of repair actions.
- Do not decide which side should be modified.
- Do not label evidence conflicts as `correct`, `incorrect`, `ready`, `blocked`, `source of truth`, gaps, or repair actions.
- Do not recommend `sync` or `build` as the default next task for discovery questions.
- Suggested follow-up should be read-only or interpretive: inspect runtime behavior, check tests, compare versions, ask owner, broaden search, or treat the claim as a hypothesis.
- If the user asks who should change, whether something is acceptable, whether it violates intent, or whether to fix docs/code, route to `review`.

Use this structure for non-trivial output:

- `Explore Frame`: question, inquiry type, checked scope, downstream use, and stop rule.
- `Observed Answer`: direct descriptive answer before evidence details. Use only the relevant subfields: `How It Works`, `What Exists`, `Differences Observed`, `Entrypoints / Flow`, `Not Found In Checked Scope`, and `Not Checked`.
- `Evidence Basis`: source-backed facts supporting the observed answer.
- `Evidence Map`: source -> fact -> observed implication.
- `Reliability / Not Checked`: claims that are contradicted, weak, stale-looking, version-sensitive, unsafe to rely on, or outside checked scope.
- `Evidence Probes`: temporary non-mutating probes used to establish facts, including side-effect checks.
- `Missing Evidence`: evidence not found or not checked; say "no evidence found for X in checked scope" rather than "X is missing" when no baseline verdict was requested.
- `Evidence Sufficiency`: whether the evidence is sufficient, partial, or insufficient for `shape`, `review`, and exceptional `plan` use.
- `Downstream Use`: shape-ready evidence, review-ready evidence, plan-ready evidence, and missing evidence for each downstream task.
- `Unknowns`: missing facts or weak evidence.
- `Constraints Found`: boundaries, existing behavior, dependencies, or doc constraints.
- `Candidate Interpretations`: plausible explanations or readings of the evidence.
- `Likely Entry Points`: probable files, APIs, flows, or docs to inspect next.
- `Borrowable Ideas`: patterns or structures that may be useful later, without recommending adoption.
- `Follow-up Targets`: sources, probes, runtime observations, or questions that could strengthen evidence later.
- `Candidate Review Targets`: questions that may need `review` if the user wants a verdict, source-of-truth decision, baseline gap review, or change judgment. Do not include a verdict.
- `Recommended Next Task`: default to `shape`, `review`, `persist`, `distill`, or `none`. Recommend `plan` only when the direction or target is already selected and the evidence merely supplies repo-aware planning context. Do not recommend `sync` from discovery unless the user explicitly asks for stable-document projection and review/source-of-truth prerequisites are already clear.

Lens use must not change task responsibility. `architecture`, `boundary`, `debug`, and `language` may improve evidence extraction; `boundary` may focus evidence on imports, call direction, contract terms, provider logic inventory, package API dependencies, and suspected leakage. `explore` must not present candidate interpretations or suspected leakage as final synthesis or verdict.

Use this shape for difference reliability notes:

```text
Reliability / Not Checked:
- Claim: <what docs, examples, tests, or comments claim>
- Evidence: <code, docs, tests, examples, behavior, or version evidence>
- Difference Observed: <what does not line up>
- Reliability: <reliable fact | weak signal | hypothesis only | do not rely>
- Possible Explanation: <version drift, stale docs, optional path, incomplete sample, unknown>
- Suggested Follow-up: <inspect runtime behavior | check tests | ask owner | compare versions | treat as hypothesis>
```

## Compact Output By Default

In `Mode: discuss`, default to:

```text
User Intent: <one line about what the user wants to understand>
Explore Frame:
- Explore Question: <what the user wants to understand>
- Inquiry Type: how-it-works | what-exists | difference-map | evidence-check | entrypoint-map
- Source Scope: <checked files/docs/commands/materials>
- Downstream Use: shape | review | plan | none
- Stop Rule: <when enough descriptive evidence has been collected>
Observed Answer:
- How It Works: <only when relevant; observed behavior or mechanism>
- What Exists: <only when relevant; source-backed inventory>
- Differences Observed: <only when relevant; observed differences without judging which side is correct>
- Entrypoints / Flow: <only when relevant; observed entrypoints or flow>
- Not Found In Checked Scope: <evidence not found, with checked scope>
- Not Checked: <scope not checked>
Evidence Basis:
- <source -> observed fact>
Reliability / Not Checked:
- <0-3 reliability notes, weak evidence, or unchecked scope notes>
Evidence Probes:
- <optional; probe, command or method, observed result, reliability, side effect check>
Evidence Sufficiency:
- For Shape: <sufficient|partial|insufficient>
- For Review: <sufficient|partial|insufficient>
- For Plan: <sufficient|partial|insufficient|not-applicable>
Downstream Use:
- Shape-ready Evidence: <evidence or none>
- Review-ready Evidence: <evidence or none>
- Plan-ready Evidence: <only when direction/target is selected; otherwise not-applicable>
- Missing For Shape: <missing evidence or none>
- Missing For Review: <missing evidence or none>
- Missing For Plan: <missing repo facts or not-applicable>
Follow-up Targets:
- <0-3 sources, probes, or candidate review targets>
Candidate Review Targets:
- <reviewable question or none>
Recommended Next Task: <shape|review|persist|distill|none; plan only when direction/target is already selected>
Persist Candidate: Artifact=note; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

Use `Persist Candidate: none` when the exploration is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to organize, refine, or prepare evidence for persist without writing files:

```text
User Intent: <one line about what the user wants to understand>
Explore Frame:
- Explore Question: <what the user wants to understand>
- Inquiry Type: how-it-works | what-exists | difference-map | evidence-check | entrypoint-map
- Source Scope: <checked files/docs/commands/materials>
- Downstream Use: shape | review | plan | none
- Stop Rule: <when enough descriptive evidence has been collected>
Observed Answer:
- How It Works: <only when relevant>
- What Exists: <only when relevant>
- Differences Observed: <only when relevant>
- Entrypoints / Flow: <only when relevant>
- Not Found In Checked Scope: <evidence not found, with checked scope>
- Not Checked: <scope not checked>
Evidence Basis:
- <source-backed fact, reliability status, and observed implication>
Candidate Interpretations:
- <plausible explanation, borrowable idea, or likely entrypoint to preserve>
Evidence Probes:
- <probe, command or method, observed result, reliability, side effect check, or none>
Reliability / Not Checked:
- <evidence not found, weak evidence, stale-looking evidence, or unchecked scope>
Evidence Sufficiency:
- For Shape: <sufficient|partial|insufficient>
- For Review: <sufficient|partial|insufficient>
- For Plan: <sufficient|partial|insufficient|not-applicable>
Downstream Use:
- Shape-ready Evidence: <evidence or none>
- Review-ready Evidence: <evidence or none>
- Plan-ready Evidence: <only when direction/target is selected; otherwise not-applicable>
- Missing For Shape: <missing evidence or none>
- Missing For Review: <missing evidence or none>
- Missing For Plan: <missing repo facts or not-applicable>
Follow-up Targets:
- <source, probe, runtime observation, user question, or candidate review target>
Discussion Notes To Preserve:
- <source, reliability note, contradiction, example, or constraint worth preserving>
Open Questions:
- <missing source or evidence gap>
Recommended Next Task:
- <shape|review|persist|distill|none; plan only when direction/target is already selected>
Persist Candidate:
- Artifact=note; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, or requests `Output: full`. This packet is handoff input for `persist`; it is not the final persisted artifact schema. `persist` must load the matching template and shape the final artifact.

```text
Persist Packet:
Artifact: note
Thread: <thread or none>
Topic: <topic>
Suggested Target: .session/inbox/note_<topic>.md or .session/threads/<thread>/note_<topic>.md
Source Summary: <code paths, docs, references, or materials explored>
Key Fields:
- Explore Frame: <question, inquiry type, checked scope, downstream use, and stop rule>
- Observed Answer: <how it works, what exists, differences observed, entrypoints or flow, not found in checked scope, and not checked>
- Evidence Basis: <main source-backed facts>
- Candidate Interpretations: <plausible explanations or entrypoints; not final direction>
- Evidence Probes: <temporary non-mutating probes and side-effect checks, or none>
- Reliability / Not Checked: <evidence strength, observed differences, weak evidence, unknowns, and unchecked scope>
- Evidence Sufficiency: <For Shape, For Review, For Plan>
- Downstream Use: <shape-ready evidence, review-ready evidence, exceptional plan-ready evidence, and missing evidence for each>
- Follow-up Targets: <sources, probes, or candidate review targets>
- Constraints Found: <constraint or boundary found>
Next Use: <shape | review | plan | persist | none; plan only when direction/target is already selected>
```

If the exploration is not worth preserving, output `Persist Candidate: none`.

## User Input

{{question, source material, code area, or reference to explore}}
