---
id: explore
role: designer
purpose: Extract evidence about what exists, where it is, how it appears to work, and how reliable the evidence is.
inputs:
  - question_or_source
outputs:
  - chat_findings
  - persist_hint
  - full_persist_packet
user_selectable_lenses:
  - architecture
  - debug
  - language
done_check:
  - evidence_is_named
  - unknowns_are_listed
  - next_decision_is_clear
---

# Explore Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/designer.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must restate what the user wants, not summarize technical facts.
- `Mode: discuss` is default and is the only valid mode for this task.
- Do not load templates and do not write files.
- If the user asks to persist, provides a target, or sets `Output: full`, return `Full Persist Packet` and route the write to `persist`.
- `Mode: execute` is not valid for this task.

## When To Use

- Use when the user clearly needs evidence about current code, docs, behavior, feasibility, reference material, entrypoints, dependencies, or unknowns.
- Use when the user asks what exists, where something is, how something appears to work, whether a capability exists, or how reliable the evidence is.
- Use when the user needs to understand a repository or material before deciding whether to borrow, maintain, review, plan, or redesign anything.
- Use when `shape` cannot safely recommend a direction because missing facts could change the answer.
- Use before `shape` or `plan` when the decision depends on repository reality or external references.

## Do Not Use When

- Do not use to make the final direction decision; use `shape`.
- Do not use for ambiguous what-if, route-comparison, concept-level, or direction-setting requests unless the user primarily asks for evidence.
- Do not use to judge whether a plan, diff, or implementation is acceptable; use `review`.
- Do not use to decide whether docs or code should be modified; use `review` for verdicts and `sync` or `plan/build` only after that verdict.
- Do not use to create executable steps; use `plan`.
- Do not use to write session artifacts; use `persist`.
- Do not use to update project docs; use `sync`.
- Do not use to summarize or distill specified source material; use `distill`.

## Boundary Layers

- `Core Responsibility`: extract source-backed evidence about what exists, where it is, how it appears to work, and how reliable the evidence is.
- `Adjacent Allowance`: include candidate interpretations, likely entrypoints, borrowable ideas, potential options, reliability follow-up, and a recommended next task when they help the user use the evidence.
- `Forbidden Authority`: do not choose the final direction, issue a verdict, decide source of truth, write a repair plan, create implementation sequencing, stable-sync documents, write files, execute, or implement.

Adjacent allowance must stay evidence-grounded. If the adjacent output becomes the main deliverable, route to `shape`, `review`, `distill`, or `plan`.

## Expected Output

- `Sources Checked`, `Observed Facts`, `Evidence Map`, `Reliability Notes`, `Unknowns`, `Constraints Found`, `Potential Options`, and `Recommended Next Task`.
- `Output: compact` default: short findings, key reliability notes, and optional `Persist Candidate`.
- `Full Persist Packet` only when findings should be persisted now or `Output: full` is requested.

## Task Boundary Check

Before exploring, classify obvious boundary problems:

- `fits`: user asks to understand code, docs, behavior, feasibility, reference material, entrypoints, dependencies, or source evidence.
- `fits_with_preflight`: request scope is too broad, source is unclear, or the request may actually belong to `review`, `shape`, or `plan`. In `Mode: discuss`, run conditional boundary preflight only.
- `composite`: user asks to explore and persist; explore first, then route to `persist`.
- `wrong_task`: user asks to choose a direction; recommend `shape`.
- `wrong_task`: user asks to produce implementation steps; recommend `plan`.
- `wrong_task`: user asks to judge a target or diff; recommend `review`.
- `wrong_task`: user asks to update project docs; recommend `sync`.
- `wrong_task`: user asks to summarize, distill, or compress specified source material; recommend `distill`.

Conditional implicit preflight for `explore` only checks boundary, source, scope, and evidence type. Do not duplicate exploration inside preflight; once the boundary is clear, proceed with normal evidence extraction or recommend the right task.

Boundary handling:

- `fits`: extract evidence in chat.
- `fits_with_preflight`: run the boundary/source/scope/evidence-type preflight, then either extract evidence or route to the right task.
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

Explore enough to reduce uncertainty for the next decision. Separate observed facts from assumptions and inferences. You may provide candidate interpretations, likely entrypoints, and borrowable ideas, but do not present them as the final direction or a verdict; prepare evidence for `shape`, `review`, or `plan`.

## Discovery vs Judgment Rule

Do not infer repository ownership or maintenance responsibility. Choose `explore` vs `review` from the user's question type.

- `explore` answers what exists, where it is, how it appears to work, and how reliable the evidence is.
- `review` answers whether something is acceptable, correct, consistent, ready, worth changing, or which source should be treated as truth.
- For code/docs/test/example mismatches during exploration, record `Reliability Notes` instead of repair actions.
- Do not decide which side should be modified.
- Do not recommend `sync` or `build` as the default next task for discovery questions.
- Suggested follow-up should be read-only or interpretive: inspect runtime behavior, check tests, compare versions, ask owner, broaden search, or treat the claim as a hypothesis.
- If the user asks who should change, whether something is acceptable, whether it violates intent, or whether to fix docs/code, route to `review`.

Use this structure for non-trivial output:

- `Sources Checked`: code paths, docs, references, commands, or materials reviewed.
- `Observed Facts`: facts directly supported by checked sources.
- `Evidence Map`: source -> fact -> implication.
- `Reliability Notes`: claims that are contradicted, weak, stale-looking, version-sensitive, or unsafe to rely on.
- `Unknowns`: missing facts or weak evidence.
- `Constraints Found`: boundaries, existing behavior, dependencies, or doc constraints.
- `Candidate Interpretations`: plausible explanations or readings of the evidence.
- `Likely Entry Points`: probable files, APIs, flows, or docs to inspect next.
- `Borrowable Ideas`: patterns or structures that may be useful later, without recommending adoption.
- `Potential Options`: candidate materials for `shape`; these are not final recommendations.
- `Recommended Next Task`: usually `shape`, `review`, `plan`, or `persist`. Recommend `sync` only when the user explicitly asks for stable-document projection and the required review/source-of-truth prerequisites are already clear.

Lens use must not change task responsibility. `architecture`, `debug`, and `language` may improve evidence extraction, but `explore` must not present candidate interpretations as final synthesis or verdict.

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
Recommended Next Task: <shape|review|plan|persist|distill|none>
Persist Candidate: Artifact=<note|option>; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
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
Discussion Notes To Preserve:
- <source, reliability note, contradiction, example, or constraint worth preserving>
Open Questions:
- <missing source or evidence gap>
Recommended Next Task:
- <shape|review|plan|persist|distill|none>
Persist Candidate:
- Artifact=<note|option>; Thread=<thread or none>; Topic=<topic>; Suggested Target=<path>
```

## Full Persist Packet

Output the full packet only when the user asks to persist, provides `Target`, or requests `Output: full`. This packet is handoff input for `persist`; it is not the final persisted artifact schema. `persist` must load the matching template and shape the final artifact.

```text
Persist Packet:
Artifact: note | option
Thread: <thread or none>
Topic: <topic>
Suggested Target: .session/inbox/<artifact>_<topic>.md or .session/threads/<thread>/<artifact>_<topic>.md
Source Summary: <code paths, docs, references, or materials explored>
Key Fields:
- Evidence Summary: <main source-backed facts>
- Sources Checked: <source list>
- Candidate Interpretations: <plausible explanations or entrypoints; not final direction>
- Reliability Notes: <evidence strength, conflicts, and unknowns>
- Constraints Found: <constraint or boundary found>
Next Use: <shape | plan | review | persist | sync | none>
```

If the exploration is not worth preserving, output `Persist Candidate: none`.

## User Input

{{question, source material, code area, or reference to explore}}
