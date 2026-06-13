# Lenses

Lenses are user-selected thinking tools. They are not stages, modes, or automatic behavior.

Default to `Lens: none`. Add a lens only when the user explicitly names it or adds the file as context.

- `distill`: extract reusable structure and writing principles from strong reference documents.
- `language`: control output language, terminology consistency, and project glossary updates.
- `domain`: review-owned lens for terms, rules, ownership, boundaries, events, and conceptual model verdicts.
- `redteam`: deliberately challenge assumptions, risks, boundaries, and failure modes.
- `test`: strengthen verification and acceptance evidence.
- `architecture`: inspect boundaries, dependencies, interfaces, constraints, and structural tradeoffs.
- `debug`: diagnose defects or uncertain runtime behavior.
- `consistency`: triangulate code, project docs, session decisions, tests, README files, workflow artifacts, prompts, templates, and archive summaries for drift.

Folded concepts:

- `strategy`: built into `shape` option comparison.
- `conceptual`: built into core `Abstraction Level` rules.
- `iteration`: built into session/thread inference and `persist` rules.
- `expand`: built into `Output` and `Depth` behavior.

Use `persist` for active session outputs: `.session/inbox/**` for staged inputs and `.session/threads/{thread}/**` for related shape, plan, review, decision, and reference artifacts. Code-aligned project knowledge goes to `docs/**` through `sync`; archive summaries go to `.session/archive/<thread>/summary.md` through `sync`. Code-adjacent reading notes go to `src/**/README.md`.
