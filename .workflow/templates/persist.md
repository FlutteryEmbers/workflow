# Persist Operation

Use this as the `persist` task operation checklist. It is not the persisted artifact body.

## Inputs

- Artifact: {{brief | note | shape | option | plan | review | decision | distillation | expanded}}
- Brief Type: {{general | external-goal; only for Artifact: brief}}
- Artifact State: {{inbox | working | settled | superseded}}
- Thread: {{kebab-case small closable work item or none}}
- Intent: {{summary | exploration | decision | audit | handoff | constraint | reference | capture}}
- Depth: {{compact | standard | detailed}}
- Topic: {{file-safe topic}}
- Source: {{Persist Packet | Persist Candidate | recent discussion | existing artifact | file path | user input}}
- Target: {{explicit target or inferred target}}

## Rules

- Preserve decision-relevant reasoning, not full transcript.
- Keep source context, key facts, decision trail, rejected options, risks, unknowns, examples, and next use when they affect later work.
- For `shape`, `plan`, `decision`, `review`, and `option` artifacts, preserve selected discussion memory in `Discussion Notes To Preserve` when it affects future understanding, revision, implementation, or audit.
- Prefer preserving useful nuance over forcing every discussion point into a predefined field.
- `Execution Trace` from `build` may be persisted as `Artifact: note` with `Intent: audit` for current-work-item audit.
- Reusable build execution discoveries may be persisted as `Artifact: note`, `Artifact State: inbox`, and `Intent: capture`.
- If the source is only a compact execution trace, do not invent missing details; mark missing fields as `unknown`.
- Separate confirmed facts from assumptions.
- Do not compress detailed discussion into unsupported conclusions.
- Use the artifact template that matches `Artifact`.
- Apply explicit review edits only; do not invent a new direction, plan, or verdict.
- Infer targets for `.session/inbox/**` or `.session/threads/{thread}/{artifact}_{topic}.md`.
- Treat inbox capture as untriaged working memory, not source of truth or an execution source.
- Use `Target Directory` when the user wants a specific thread folder.
- Infer thread targets by same-work-item fit when `Thread` is absent.
- Reuse active threads for continuations, refinements, corrections, review responses, or implementation follow-ups for the same closable work item.
- Use related old or closed threads as `Source Context` unless they pass the same-work-item test.
- Include `Thread Inference Note` when inference depends on assumptions or low-confidence fit.
- Route `docs/**`, `src/**/README.md`, and `.session/archive/**` to `sync`.
- Route code, `.workflow/**`, and `.github/**` to `build` or external-agent.

## Revision Boundary

- Allowed: restructure, clarify wording, merge explicit review edits, and preserve rejected options or unresolved risks.
- Not allowed: choose a new shape, re-plan execution, judge whether review feedback is correct, or promote unclear `needs changes` content.
- If revision requires new judgment, route to `shape`, `plan`, or `review` before persisting.

## Depth

- `compact`: short artifact with why it matters, useful facts, and next step.
- `standard`: structured artifact with source context, key points, facts, assumptions, open questions, and next use.
- `detailed`: high-fidelity artifact with decision trail, evidence, alternatives, rejected options, risks, examples or pseudocode, validation, and next use.

## Output

- Target: {{resolved target}}
- Naming Note: {{only if explicit target differs from recommended prefix}}
- Thread Inference Note: {{same-work-item basis, source context, low-confidence assumption, or none}}
- Persisted Artifact: {{summary of what was written}}
