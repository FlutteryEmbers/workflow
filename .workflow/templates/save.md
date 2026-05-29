# Save Operation

Use this as the `save` task operation checklist. It is not the saved artifact body.

## Inputs

- Artifact: {{brief | note | shape | option | plan | review | decision | distillation | expanded | goal}}
- Status: {{inbox | draft | accepted}}
- Style: {{summary | exploration | audit | constraint | handoff}}
- Topic: {{file-safe topic}}
- Source: {{recent discussion | existing artifact | file path | user input}}
- Target: {{explicit target or inferred target}}

## Rules

- Preserve decision trail, not full transcript.
- Use the artifact template that matches `Artifact`.
- Infer targets only for `.session/inbox/**` and `.session/drafts/**`.
- Require explicit accepted intent before writing `.session/accepted/**`.
- Route `docs/**` and `src/**/README.md` to `sync`.
- Route code, `.workflow/**`, and `.github/**` to `build` or external-agent.

## Output

- Target: {{resolved target}}
- Naming Note: {{only if explicit target differs from recommended prefix}}
- Saved Artifact: {{summary of what was written}}
