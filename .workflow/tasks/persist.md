---
id: persist
role: steward
purpose: Persist high-fidelity structured session artifacts from discussion, thread artifacts, Persist Packets, or user-provided sources.
inputs:
  - artifact
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

- Start with an inline `Understanding` unless the request is trivial.
- `Mode: persist` is required for writes.
- `Mode: discuss` may propose a persist packet, target, artifact kind, status, intent, depth, and template without writing.
- `Mode: execute` is not valid for this task.
- Load templates only for the artifact being persisted.

## When To Use

- Use after `clarify`, `explore`, `shape`, `plan`, or `review` discussion when the user wants to persist a session artifact.
- Use to update an existing `.session/**` artifact with newer discussion, review feedback, or user corrections.
- Use to persist structured artifacts into `.session/threads/{thread}/**` so related shape, plan, review, and decision files stay together.
- Use to write disposable exploration notes only when the user explicitly provides `Target: notes/**`.

## Do Not Use When

- Do not write project docs; use `sync` for `docs/**` and `src/**/README.md`.
- Do not modify source code, `.workflow/**`, `.github/**`, or other repository artifacts; use `build` or external-agent.
- Do not persist full transcripts by default.

## Persist Inputs

- `Artifact`: `brief | note | shape | option | plan | review | decision | distillation | expanded | goal`.
- `Status`: `inbox | working | stable | superseded`.
- `Intent`: `summary | exploration | decision | audit | handoff | constraint | reference`.
- `Depth`: `compact | standard | detailed`.
- `Thread`: kebab-case discussion or work-thread directory name.
- `Topic`: short file-safe topic.
- `Source`: `Persist Packet`, recent discussion, existing artifact, user input, file path, or selected context.
- `Target Directory`: optional directory such as `.session/threads/auth-backend/`; when present, generate `{artifact}_{topic}.md` under it.
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
- `detailed`: include reasoning trail, evidence, alternatives considered, rejected options, risks, examples or pseudocode when useful, validation approach, and next use.
- Default `Depth: standard` for `brief` and `note`.
- Default `Depth: detailed` for `shape`, `option`, `plan`, `review`, `decision`, `distillation`, and `expanded`.
- Use a lower depth only when the user explicitly asks for a compact artifact.

## Source Handling

- Prefer explicit source boundaries in this order: explicit `Source` or file path; explicit `Artifact ID` reference such as `shape_<topic>`; explicit `Persist Packet`; main goal from recent discussion; latest topic only when explicitly targeted.
- Prefer an explicit `Persist Packet` when present.
- If no `Persist Packet` exists, synthesize one from recent discussion, source artifacts, user corrections, and selected files.
- If source material conflicts, preserve the conflict and mark the source of truth as unresolved.
- Do not use `persist` to decide product direction, approve plans, or resolve code/docs drift; route those decisions back to `shape`, `plan`, or `review`.

## Main Goal Bias

When persisting from a multi-topic discussion, prefer the original or recurring main goal over the most recent topic.

- If the user references an `Artifact ID` such as `shape_<topic>`, persist that artifact as the primary source.
- Treat later topics as supporting context unless the user explicitly asks to persist the latest topic.
- If the discussion includes multiple topics and no main goal can be inferred from `Artifact ID`, `Topic`, `Target`, or explicit request wording, return `missing_prerequisite` and ask for one of those anchors before writing.
- Example: after discussing backend design and then frontend integration, `persist shape_auth_backend` should persist the backend shape; frontend integration belongs only in supporting context unless explicitly targeted.

## Persist Revision Rule

`persist` may revise artifact form and apply explicit review or user edits, but it must not make new design, planning, or source-of-truth judgments.

Allowed:

- Restructure an artifact using the matching template.
- Improve wording, metadata, reasoning trail, examples, and traceability.
- Merge an existing artifact with explicit review findings or user-confirmed edits.
- Move rejected or invalidated content into rejected options, risks, or open questions.

Not allowed:

- Choose a new direction, boundary, or architecture.
- Reorder an implementation plan based on new judgment.
- Decide whether a review finding is correct.
- Turn an unclear `needs changes` review into a stable artifact.

If review feedback is not explicit enough to apply mechanically, route back to `shape`, `plan`, or `review` before persisting.

## Target Rules

- Infer `.session/inbox/{artifact}_{topic}.md` for `Status: inbox`.
- Infer `.session/threads/{thread}/{artifact}_{topic}.md` when `Thread`, `Artifact`, and `Topic` are available.
- If `Target Directory` is provided, infer `{target_directory}/{artifact}_{topic}.md`.
- If the user references `Artifact ID: shape_<topic>` without an explicit thread, infer `.session/threads/<topic-kebab>/shape_<topic>.md`.
- Never infer `notes/**`. Write `notes/**` only when the user explicitly provides that target.
- Write `.session/goal/**` only when the user explicitly provides that target.
- Respect explicit `.session/**` targets even when the file name does not follow the recommended prefix; include a naming note instead of blocking.
- If `Artifact`, `Thread`, `Topic`, `Target Directory`, `Target`, and `Artifact ID` are insufficient to infer a target, return `missing_prerequisite` and ask for one anchor.

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

- `.session/**`: `persist` may write it when the request is a session artifact.
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
- `goal`: `.workflow/templates/goal.md`

## Output Rules

- Write only the target session artifact or explicit `notes/**` exploration note.
- Add `Persist Metadata` to the artifact.
- Keep `Intent` and `Depth` as metadata; do not use them to choose directories or permissions.
- `Status` is metadata only. It does not authorize execution and does not choose directories.
- Thread artifacts are session working memory; code-aligned project docs still go through `sync`.
- The persisted artifact should be denser and more durable than chat. It must not be a low-context bullet summary when detailed reasoning is available.
- For `notes/**`, compact or standard depth is acceptable. Preserve useful conclusion, evidence, and next use, but do not treat the note as an implementation source.

## User Input

{{artifact, status, intent, depth, thread, topic, source, target directory, target, and persist request}}
