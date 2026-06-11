---
id: persist
role: steward
purpose: Persist high-fidelity structured session artifacts from discussion, thread artifacts, Persist Packets, or user-provided sources.
inputs:
  - artifact
  - brief_type
  - status
  - intent
  - depth
  - thread
  - topic
  - source
  - target_directory
  - target
  - request
outputs:
  - .session/inbox/{artifact}_{topic}.md
  - .session/threads/{thread}/{artifact}_{topic}.md
  - notes/{topic}.md
user_selectable_lenses:
  - iteration
  - language
done_check:
  - artifact_kind_is_named
  - status_is_clear
  - target_boundary_is_valid
---

# Persist Task

## Context Injection

Role: {{CONTENT: /.workflow/roles/steward.md}}

## Mode Rules

- Start with `User Intent` unless the request is trivial; it must state what the user wants persisted.
- `Mode: persist` is required for writes.
- `Mode: discuss` may propose a persist packet, target, artifact kind, artifact state, intent, depth, and template without writing.
- `Mode: execute` is not valid for this task.
- Load templates only for the artifact being persisted.

## When To Use

- Use after `clarify`, `explore`, `shape`, `plan`, or `review` discussion when the user wants to persist a session artifact.
- Use to update an existing `.session/**` artifact with newer discussion, review feedback, or user corrections.
- Use to persist structured artifacts into `.session/threads/{thread}/**` so related shape, plan, review, and decision files stay together.
- Use to write disposable exploration notes only when the user explicitly provides `Target: notes/**`.

## Do Not Use When

- Do not write stable sync targets; use `sync` for `docs/**`, `src/**/README.md`, and `.session/archive/**`.
- Do not modify source code, `.workflow/**`, `.github/**`, or other repository artifacts; use `build` or external-agent.
- Do not persist full transcripts by default.

## Persist Inputs

- `Artifact`: `brief | note | shape | option | plan | review | decision | distillation | expanded`.
- `Brief Type`: optional for `Artifact: brief`; use `external-goal` for long, external, or reusable goal material stored in `.session/inbox/**`.
- `Artifact State`: `inbox | working | settled | superseded`.
- `Intent`: `summary | exploration | decision | audit | handoff | constraint | reference`.
- `Depth`: `compact | standard | detailed`.
- `Thread`: kebab-case small closable work item directory name.
- `Topic`: short file-safe topic.
- `Source`: `Persist Packet`, `Persist Candidate`, recent discussion, existing artifact, user input, file path, or selected context.
- `Target Directory`: optional directory such as `.session/threads/workflow-thread-naming/`; when present, generate `{artifact}_{topic}.md` under it.
- `Target`: optional explicit path. Explicit target wins over inferred path.
- `Target: notes/**`: explicit only; used for disposable exploration notes.

Canonical fields:

```text
Intent: summary | exploration | decision | audit | handoff | constraint | reference
Depth: compact | standard | detailed
```

## Content Fidelity Contract

Persisted artifacts must be more structured than chat without losing the reasoning needed for future work.

- Preserve decision-relevant reasoning: context, constraints, evidence, user corrections, option tradeoffs, rejected options, examples, pseudocode, risks, unknowns, and next use.
- Do not compress multi-turn discussion into unsupported conclusions.
- Remove conversational noise, repetition, stale intermediate phrasing, and obvious dead ends.
- Separate confirmed facts from assumptions or AI-added inferences.
- If the source is thin, say what is missing instead of inventing confidence.
- Full transcript is not the default. If the user asks to persist the process, write a conversation summary. Persist verbatim transcript only when explicitly requested and label it noisy and not source of truth.

## Depth Rules

- `compact`: keep the artifact short, but include why it matters, useful facts, and next step.
- `standard`: include source context, key points, decision-relevant facts, assumptions, open questions, and next use.
- `detailed`: include decision trail, evidence, alternatives considered, rejected options, risks, examples or pseudocode when useful, validation approach, and next use.
- Default `Depth: standard` for `brief` and `note`.
- Default `Depth: detailed` for `shape`, `option`, `plan`, `review`, `decision`, `distillation`, and `expanded`.
- Use a lower depth only when the user explicitly asks for a compact artifact.

## Source Handling

- Prefer explicit source boundaries in this order: explicit `Source` or file path; explicit `Artifact ID` reference such as `shape_<topic>`; explicit `Persist Packet`; explicit `Persist Candidate`; source artifacts from the inferred same work item; recent discussion that matches the target work item.
- Prefer an explicit `Persist Packet` when present.
- If no `Persist Packet` exists, synthesize one from `Persist Candidate`, recent discussion, source artifacts, user corrections, and selected files.
- Discuss output budget does not reduce artifact fidelity. Even if the prior answer used `Output: compact`, generate the persisted artifact at the requested or default `Depth`.
- If source material conflicts, preserve the conflict and mark the source of truth as unresolved.
- Do not use `persist` to decide product direction, approve plans, or resolve code/docs drift; route those decisions back to `shape`, `plan`, or `review`.

## Thread Inference

Infer thread targets automatically by same-work-item fit.

- `Thread` names should represent small closable work items, not tasks, lenses, modes, artifact kinds, or broad feature areas.
- Prefer `{area}-{work-item}` when it improves retrieval, but do not enforce a category prefix table.
- Reuse an active thread when the request is a continuation, refinement, correction, review response, or implementation follow-up for the same closable work item.
- Reuse is supported when the request shares the same target, success criteria, blocking decision, source artifact, or natural archive summary.
- Create or suggest a new thread when the request introduces a distinct bounded decision, change, or question; has different success criteria or close condition; or would make the archive summary unclear.
- Recency is supporting evidence only. It is never sufficient by itself.
- Related old threads are `Source Context` unless they pass the same-work-item test.
- Settled, implemented, superseded, abandoned, blocked, or archived threads are `Source Context` by default and must not be reopened automatically.
- If the user references an `Artifact ID` such as `shape_<topic>`, use that artifact as source context. Do not derive the thread directory from `<topic>`.
- If confidence is low but safe, infer the best target and include `Thread Inference Note` with assumptions and risk.
- Return `missing_prerequisite` only when ambiguity would risk overwriting, mixing unrelated work items, or writing into a closed/archive thread.

## Persist Revision Rule

`persist` may revise artifact form and apply explicit review or user edits, but it must not make new design, planning, or source-of-truth judgments.

Allowed:

- Restructure an artifact using the matching template.
- Improve wording, metadata, decision trail, examples, and traceability.
- Merge an existing artifact with explicit review findings or user-confirmed edits.
- Move rejected or invalidated content into rejected options, risks, or open questions.

Not allowed:

- Choose a new direction, boundary, or architecture.
- Reorder an implementation plan based on new judgment.
- Decide whether a review finding is correct.
- Turn an unclear `needs changes` review into a settled artifact.

If review feedback is not explicit enough to apply mechanically, route back to `shape`, `plan`, or `review` before persisting.

## Target Rules

- Infer `.session/inbox/{artifact}_{topic}.md` for `Artifact State: inbox`.
- Infer `.session/threads/{thread}/{artifact}_{topic}.md` when `Thread`, `Artifact`, and `Topic` are available.
- If `Target Directory` is provided, infer `{target_directory}/{artifact}_{topic}.md`.
- If the user references `Artifact ID: shape_<topic>` without an explicit thread, use it as source context and infer the target by same-work-item fit.
- Never infer `notes/**`. Write `notes/**` only when the user explicitly provides that target.
- Respect explicit active `.session/inbox/**` or `.session/threads/**` targets even when the file name does not follow the recommended prefix; include a naming note instead of blocking.
- Route `.session/archive/**` targets to `sync` with `Sync Domain: session-archive`.
- If `Artifact`, `Thread`, `Topic`, `Target Directory`, `Target`, source context, and same-work-item inference are insufficient to infer a safe target, return `missing_prerequisite` and ask for one anchor.

## Exploration Notes Rule

`notes/**` is disposable exploration memory for lightweight exploration repos or one-off exploratory work.

- `persist` may write `notes/**` only when `Target: notes/**` is explicit.
- `notes/**` is not project docs and does not use Project Docs Rules.
- `notes/**` is not an execution source and must not be treated as an implementation plan.
- `notes/**` may be overwritten, deleted, parked, discarded, or promoted later.
- Useful conclusions from `notes/**` should be promoted through normal workflow: persist to `.session/threads/**`, or sync confirmed project context to `docs/**`.
- Optional note metadata may be used when helpful: `status`, `source`, `updated`, `promoted_to`.
- `notes/**` is suitable for `Artifact: brief | note | shape | option | review | distillation | expanded`; do not use it for implementation plans or project docs.

## Explicit Target Override

If the user explicitly provides a target path, respect it unless it violates write safety, lacks required prerequisites, or belongs to another write boundary.

- `.session/inbox/**`, `.session/threads/**`: `persist` may write them when the request is an active session artifact.
- `.session/archive/**`: route to `sync` with `Sync Domain: session-archive`.
- `notes/**`: `persist` may write it only as an explicit disposable exploration note target.
- `docs/**` or `src/**/README.md`: route to `sync` and require Project Docs Rules.
- Source code, `.workflow/**`, `.github/**`, or other repository artifacts: route to `build` or external-agent with an explicit executable plan.

## Conversation Handling

- Preserve decision trail and decision-relevant discussion content, not the full transcript.
- Include why the artifact exists, why the recommendation changed, what evidence mattered, why options were rejected, what remains unresolved, and how the artifact should be used next.

## Template Selection

- `brief`: `.workflow/templates/brief.md`
- `note`: `.workflow/templates/note.md`
- `shape`: `.workflow/templates/shape.md`
- `option`: `.workflow/templates/options.md`
- `plan`: `.workflow/templates/plan.md`
- `review`: `.workflow/templates/review.md`
- `decision`: `.workflow/templates/decision.md`
- `distillation`: `.workflow/templates/distillation.md`
- `expanded`: `.workflow/templates/expanded.md`

## Output Rules

- Write only the target `.session/inbox/**` or `.session/threads/**` artifact, or explicit `notes/**` exploration note.
- Add `Persist Metadata` to the artifact.
- Include `Thread Inference Note` when thread selection depends on same-work-item assumptions, source context, or low-confidence inference.
- Keep `Intent` and `Depth` as metadata; do not use them to choose directories or permissions.
- `Artifact State` is metadata only. `settled` does not authorize execution, does not mean approved, and does not choose directories.
- Thread artifacts are active session working memory; code-aligned project docs and session archive summaries still go through `sync`.
- The persisted artifact should be denser and more durable than chat. It must not be a low-context bullet summary when detailed reasoning is available.
- For `notes/**`, compact or standard depth is acceptable. Preserve useful conclusion, evidence, and next use, but do not treat the note as an implementation source.

## User Input

{{artifact, status, intent, depth, thread, topic, source, target directory, target, and persist request}}
