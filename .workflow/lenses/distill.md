# Lens: Distill

Use this lens when a strong reference document, architecture folder, RFC, ADR, handbook, or knowledge base should be studied for reusable structure and writing principles.

## Add Only When Needed

- The user wants to understand why a document works, not just summarize its content.
- A reference structure may improve session decisions or project docs.
- Current outputs should be compared against a strong example.

## Distillation Rules

- Observe structure before judging.
- Extract reader journey, information types, sequencing, and maintenance rules.
- Separate source-specific context from transferable principles.
- Mark adoption recommendation as `adopt`, `adapt`, `reject`, or `revisit`.

## Output Hints

- Exploration output can be persisted to `.session/inbox/note_{source}_distillation.md`.
- Shaping output can be persisted to `.session/threads/{thread}/shape_distill_{topic}.md`.
- Adoption decisions can be persisted to `.session/threads/{thread}/decision_distill_{topic}.md`.
- Stable writing principles can later be synced into `docs/**`.
- Do not directly modify `.workflow/**`; use `shape -> plan -> build` for workflow changes.
