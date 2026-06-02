# Persist Operation

Use this as the `persist` task operation checklist. It is not the persisted artifact body.

## Inputs

- Artifact: {{brief | note | shape | option | plan | review | decision | distillation | expanded | goal}}
- Status: {{inbox | draft | accepted}}
- Intent: {{summary | exploration | decision | audit | handoff | constraint | reference}}
- Depth: {{compact | standard | detailed}}
- Topic: {{file-safe topic}}
- Source: {{Persist Packet | recent discussion | existing artifact | file path | user input}}
- Target: {{explicit target or inferred target}}

## Rules

- Preserve decision-relevant reasoning, not full transcript.
- Keep source context, key facts, reasoning trail, rejected options, risks, unknowns, examples, and next use when they affect later work.
- Separate confirmed facts from assumptions.
- Do not compress detailed discussion into unsupported conclusions.
- Use the artifact template that matches `Artifact`.
- Apply explicit accepted review edits only; do not invent a new direction, plan, or verdict.
- Infer targets only for `.session/inbox/**` and `.session/drafts/**`.
- Require explicit accepted intent before writing `.session/accepted/**`.
- Route `docs/**` and `src/**/README.md` to `sync`.
- Route code, `.workflow/**`, and `.github/**` to `build` or external-agent.

## Revision Boundary

- Allowed: restructure, clarify wording, merge explicit review edits, and preserve rejected options or unresolved risks.
- Not allowed: choose a new shape, re-plan execution, judge whether review feedback is correct, or promote unclear `needs changes` content.
- If revision requires new judgment, route to `shape`, `plan`, or `review` before persisting.

## Depth

- `compact`: short artifact with why it matters, useful facts, and next step.
- `standard`: structured artifact with source context, key points, facts, assumptions, open questions, and next use.
- `detailed`: high-fidelity artifact with reasoning trail, evidence, alternatives, rejected options, risks, examples or pseudocode, validation, and next use.

## Output

- Target: {{resolved target}}
- Naming Note: {{only if explicit target differs from recommended prefix}}
- Persisted Artifact: {{summary of what was written}}
