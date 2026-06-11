# Persist Operation

Use this as the `persist` task operation checklist. It is not the persisted artifact body.

## Inputs

- Artifact: {{brief | note | shape | option | plan | review | decision | distillation | expanded | goal}}
- Artifact State: {{inbox | working | settled | superseded}}
- Thread: {{thread-name or none}}
- Intent: {{summary | exploration | decision | audit | handoff | constraint | reference}}
- Depth: {{compact | standard | detailed}}
- Topic: {{file-safe topic}}
- Source: {{Persist Packet | Persist Candidate | recent discussion | existing artifact | file path | user input}}
- Target: {{explicit target or inferred target}}

## Rules

- Preserve decision-relevant reasoning, not full transcript.
- Keep source context, key facts, decision trail, rejected options, risks, unknowns, examples, and next use when they affect later work.
- For `shape`, `plan`, `decision`, `review`, and `option` artifacts, preserve selected discussion memory in `Discussion Notes To Preserve` when it affects future understanding, revision, implementation, or audit.
- Prefer preserving useful nuance over forcing every discussion point into a predefined field.
- `Execution Summary` from `build` may be persisted as `Artifact: note` with `Intent: audit`.
- If the source is only a compact execution summary, do not invent missing details; mark missing fields as `unknown`.
- Separate confirmed facts from assumptions.
- Do not compress detailed discussion into unsupported conclusions.
- Use the artifact template that matches `Artifact`.
- Apply explicit review edits only; do not invent a new direction, plan, or verdict.
- Infer targets for `.session/inbox/**` or `.session/threads/{thread}/{artifact}_{topic}.md`.
- Use `Target Directory` when the user wants a specific thread folder.
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
- Persisted Artifact: {{summary of what was written}}
