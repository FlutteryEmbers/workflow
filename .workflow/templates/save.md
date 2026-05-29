# Save Operation

Use this as the `save` task operation checklist. It is not the saved artifact body.

## Inputs

- Artifact: {{brief | note | shape | option | plan | review | decision | distillation | expanded | goal}}
- Status: {{inbox | draft | accepted}}
- Intent: {{summary | exploration | decision | audit | handoff | constraint | reference}}
- Depth: {{compact | standard | detailed}}
- Topic: {{file-safe topic}}
- Source: {{Save Packet | recent discussion | existing artifact | file path | user input}}
- Target: {{explicit target or inferred target}}

## Rules

- Preserve decision-relevant reasoning, not full transcript.
- Keep source context, key facts, reasoning trail, rejected options, risks, unknowns, examples, and next use when they affect later work.
- Separate confirmed facts from assumptions.
- Do not compress detailed discussion into unsupported conclusions.
- Use the artifact template that matches `Artifact`.
- Infer targets only for `.session/inbox/**` and `.session/drafts/**`.
- Require explicit accepted intent before writing `.session/accepted/**`.
- Route `docs/**` and `src/**/README.md` to `sync`.
- Route code, `.workflow/**`, and `.github/**` to `build` or external-agent.

## Depth

- `compact`: short artifact with why it matters, useful facts, and next step.
- `standard`: structured artifact with source context, key points, facts, assumptions, open questions, and next use.
- `detailed`: high-fidelity artifact with reasoning trail, evidence, alternatives, rejected options, risks, examples or pseudocode, validation, and next use.

## Output

- Target: {{resolved target}}
- Naming Note: {{only if explicit target differs from recommended prefix}}
- Saved Artifact: {{summary of what was written}}
