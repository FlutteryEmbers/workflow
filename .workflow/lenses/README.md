# Lenses

Lenses are user-selected thinking tools. They are not stages, modes, or automatic behavior.

Default to `Lens: none`. Add a lens only when the user explicitly names it or adds the file as context.

- `iteration`: maintain multi-turn session state, goal changes, current recommendation, rejected options, and open questions.
- `expand`: expand a compact decision or plan with examples, pseudocode, smaller diagrams, or split parts.
- `distill`: extract reusable structure and writing principles from strong reference documents.
- `language`: control output language, terminology consistency, and project glossary updates.
- `domain`: align terms, rules, ownership, boundaries, events, and conceptual model.
- `strategy`: compare technical routes or design options.
- `redteam`: deliberately challenge assumptions, risks, boundaries, and failure modes.
- `test`: strengthen verification and acceptance evidence.
- `architecture`: inspect boundaries, dependencies, interfaces, constraints, and structural tradeoffs.
- `debug`: diagnose defects or uncertain runtime behavior.
- `consistency`: compare code, project docs, session decisions, tests, and code-adjacent README files for drift.

Use `persist` for session outputs: `.session/inbox/**` for staged inputs and `.session/threads/{thread}/**` for related shape, plan, review, decision, and reference artifacts. Code-aligned project knowledge goes to `docs/**` through `sync`. Code-adjacent reading notes go to `src/**/README.md`.
