---
id: explore
role: designer
purpose: Acquire source-backed evidence through discovery inventory, evidence mapping, reliability assessment, and temporary non-mutating probes.
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

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must restate what the user wants, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- Evidence probes are allowed only as non-mutating observation inside `Mode: discuss`; they are not `Mode: execute`.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user clearly needs evidence about current code, docs, behavior, feasibility, reference material, entrypoints, dependencies, or unknowns.
- Use when the user asks what exists, where something is, how something appears to work, whether evidence for a capability exists, or how reliable the evidence is.
- Use when the user needs a non-mutating probe, dry-run, collection command, list command, one-off shell probe, or temporary script outside the repo to establish source-backed evidence.
- Use when the user needs to understand a repository or material before deciding whether to borrow, maintain, review, plan, or redesign anything.
- Use when `shape` cannot safely recommend a direction because missing facts could change the answer.
- Use before `shape` or `plan` when the decision depends on repository reality or external references.

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

- `Core Responsibility`: acquire source-backed evidence through discovery inventory, evidence mapping, reliability assessment, missing-evidence reporting, and temporary non-mutating probes.
- `Adjacent Allowance`: include candidate interpretations, likely entrypoints, borrowable ideas, missing evidence, follow-up targets, candidate review targets, and a recommended next task when they help the user use the evidence.
- `Hard Authority Boundaries`: no durable writes, no stable sync, no implementation or write-path execution, no source-of-truth promotion, and no verdict/severity fields.

Explore output must remain evidence-shaped. If the user asks for judgment, provide evidence plus `Candidate Review Targets`; if the user asks for direction, provide evidence plus likely `shape` inputs.

## Output Shape

`Output Shape: Evidence Map`

Explore output is shaped around evidence:

- `Observed Facts`
- `Evidence Map`
- `Evidence Probes`
- `Reliability Notes`
- `Missing Evidence`
- `Follow-up Targets`
- `Candidate Review Targets`
- `Recommended Next Task`

## Expected Output

- `Sources Checked`, `Observed Facts`, `Evidence Map`, `Evidence Probes`, `Reliability Notes`, `Missing Evidence`, `Unknowns`, `Constraints Found`, `Follow-up Targets`, and `Recommended Next Task`.
- `Output: compact` default: short findings, key reliability notes, and optional `Persist Candidate`.
- `Full Persist Packet` only when findings should be persisted now or `Output: full` is requested.

## Task Boundary Check

Before exploring, classify obvious boundary problems. Prefer an in-shape evidence response over `wrong_task` when the user-selected task can still provide useful evidence.

- `fits`: user asks to understand code, docs, behavior, feasibility, reference material, entrypoints, dependencies, source evidence, or non-mutating probe results.
- `fits_with_preflight`: request scope is too broad, source is unclear, or the request may actually belong to `review`, `shape`, or `plan`. In `Mode: discuss`, run conditional boundary preflight only.
- `composite`: user asks to explore and persist; explore first, then route to `persist`.
- `fallback_fit`: user asks for a judgment or direction but `explore` can still provide evidence-shaped output and candidate next targets.
- `wrong_task`: user asks for writing, stable sync, implementation, or a source-of-truth verdict that cannot be answered with evidence-shaped output.

Conditional implicit preflight for `explore` only checks boundary, source, scope, and evidence type. Do not duplicate exploration inside preflight; once the boundary is clear, proceed with normal evidence extraction or recommend the right task.

Boundary handling:

- `fits`: extract evidence in chat.
- `fits_with_preflight`: run the boundary/source/scope/evidence-type preflight, then either extract evidence or route to the right task.
- `fallback_fit`: output evidence-shaped material plus `Scope Interpretation`; do not output verdict fields.
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

Explore enough to reduce uncertainty for the next decision. Separate observed facts from assumptions and inferences. You may provide candidate interpretations, likely entrypoints, and borrowable ideas, but do not present them as the final direction, verdict, repair, or baseline gap review; prepare evidence for `shape`, `review`, or `plan`.

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
- For code/docs/test/example mismatches during exploration, record `Reliability Notes` instead of repair actions.
- Do not decide which side should be modified.
- Do not label evidence conflicts as `correct`, `incorrect`, `ready`, `blocked`, `source of truth`, severity labels, gaps, or repair actions.
- Do not recommend `sync` or `build` as the default next task for discovery questions.
- Suggested follow-up should be read-only or interpretive: inspect runtime behavior, check tests, compare versions, ask owner, broaden search, or treat the claim as a hypothesis.
- If the user asks who should change, whether something is acceptable, whether it violates intent, or whether to fix docs/code, route to `review`.

Use this structure for non-trivial output:

- `Sources Checked`: code paths, docs, references, commands, or materials reviewed.
- `Observed Facts`: facts directly supported by checked sources.
- `Evidence Map`: source -> fact -> implication.
- `Reliability Notes`: claims that are contradicted, weak, stale-looking, version-sensitive, or unsafe to rely on.
- `Evidence Probes`: temporary non-mutating probes used to establish facts, including side-effect checks.
- `Missing Evidence`: evidence not found or not checked; say "no evidence found for X" rather than "X is missing" when no baseline verdict was requested.
- `Unknowns`: missing facts or weak evidence.
- `Constraints Found`: boundaries, existing behavior, dependencies, or doc constraints.
- `Candidate Interpretations`: plausible explanations or readings of the evidence.
- `Likely Entry Points`: probable files, APIs, flows, or docs to inspect next.
- `Borrowable Ideas`: patterns or structures that may be useful later, without recommending adoption.
- `Follow-up Targets`: sources, probes, runtime observations, or questions that could strengthen evidence later.
- `Candidate Review Targets`: targets that may need `review` if the user wants a verdict, source-of-truth decision, or baseline gap review.
- `Recommended Next Task`: usually `shape`, `review`, `plan`, or `persist`. Recommend `sync` only when the user explicitly asks for stable-document projection and the required review/source-of-truth prerequisites are already clear.

Lens use must not change task responsibility. `architecture`, `boundary`, `debug`, and `language` may improve evidence extraction; `boundary` may focus evidence on imports, call direction, contract terms, provider logic inventory, package API dependencies, and suspected leakage. `explore` must not present candidate interpretations or suspected leakage as final synthesis or verdict.

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
User Intent: <one line about what the user wants to understand>
Current Read: <optional one line about the strongest source-backed fact>
Take:
- <3-6 bullets>
Risks/Unknowns:
- <0-3 bullets>
Candidate Interpretations:
- <0-3 plausible interpretations, not final direction>
Evidence Probes:
- <optional; probe, command or method, observed result, reliability, side effect check>
Missing Evidence:
- <0-3 facts not found or not checked>
Follow-up Targets:
- <0-3 sources, probes, or candidate review targets>
Recommended Next Task: <shape|review|plan|persist|distill|none>
Persist Candidate: Artifact=<note|shape>; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

Use `Persist Candidate: none` when the exploration is not worth preserving.

## Normal Refine Output

Use `Output: normal` when the user asks to organize, refine, or prepare evidence for persist without writing files:

```text
User Intent: <one line about what the user wants to understand>
Current Read: <optional one line about the strongest source-backed fact>
Refined Evidence:
- <evidence summary, reliability status, and recommended next task>
Candidate Interpretations:
- <plausible explanation, borrowable idea, or likely entrypoint to preserve>
Evidence Probes:
- <probe, command or method, observed result, reliability, side effect check, or none>
Missing Evidence:
- <evidence not found or not checked>
Follow-up Targets:
- <source, probe, runtime observation, user question, or candidate review target>
Discussion Notes To Preserve:
- <source, reliability note, contradiction, example, or constraint worth preserving>
Open Questions:
- <missing source or evidence gap>
Recommended Next Task:
- <shape|review|plan|persist|distill|none>
Persist Candidate:
- Artifact=<note|shape>; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, or requests `Output: full`. This packet is handoff input for `persist`; it is not the final persisted artifact schema. `persist` must load the matching template and shape the final artifact.

```text
Persist Packet:
Artifact: note | shape
Thread: <thread or none>
Topic: <topic>
Suggested Target: .session/inbox/<artifact>_<topic>.md or .session/threads/<thread>/<artifact>_<topic>.md
Source Summary: <code paths, docs, references, or materials explored>
Key Fields:
- Evidence Summary: <main source-backed facts>
- Sources Checked: <source list>
- Candidate Interpretations: <plausible explanations or entrypoints; not final direction>
- Evidence Probes: <temporary non-mutating probes and side-effect checks, or none>
- Reliability Notes: <evidence strength, conflicts, and unknowns>
- Missing Evidence: <evidence not found or not checked>
- Follow-up Targets: <sources, probes, or candidate review targets>
- Constraints Found: <constraint or boundary found>
Next Use: <shape | plan | review | persist | sync | none>
```

If the exploration is not worth preserving, output `Persist Candidate: none`.

## User Input

{{question, source material, code area, or reference to explore}}
