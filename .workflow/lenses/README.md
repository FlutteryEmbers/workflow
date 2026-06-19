# Lenses

Lenses are user-selected thinking tools. They are not stages, modes, or automatic behavior.

Default to `Lens: none`. Add a lens only when the user explicitly names it or adds the file as context.

- `language`: control output language, terminology consistency, and project glossary updates.
- `expert`: increase analysis density, expose hidden assumptions, compare opposing professional views, and remove generic output.
- `domain`: review-owned lens for terms, rules, ownership, boundaries, events, and conceptual model verdicts.
- `boundary`: inspect ownership, dependency direction, contract leakage, provider/package boundaries, provider-local business, main-system business, and migration ownership.
- `redteam`: review-owned critique posture for failure paths, counterexamples, hidden costs, and boundary breaks.
- `test`: strengthen verification and acceptance evidence.
- `architecture`: inspect boundaries, dependencies, interfaces, constraints, and structural tradeoffs.
- `debug`: diagnose defects or uncertain runtime behavior.
- `consistency`: triangulate code, project docs, session decisions, tests, README files, workflow artifacts, prompts, templates, and archive summaries for drift.

Use `boundary, consistency` together when a boundary verdict also needs source-of-truth, docs/code drift, contract/implementation alignment, or artifact alignment judgment. Do not create a mixed lens for this combination.

Folded concepts:

- `strategy`: built into `shape` option comparison.
- `conceptual`: built into `shape` concept boundaries and `plan` readiness rules.
- `iteration`: built into session/thread inference and `persist` rules.
- `expand`: built into `Output` and `Depth` behavior.

Use `persist` for active session outputs: `.session/inbox/**` for staged inputs and `.session/threads/{thread}/**` for related shape, plan, review, distillation, note, brief, and reference artifacts. Code-aligned project knowledge goes to `docs/**` through `sync`; archive summaries go to `.session/archive/<thread>/summary.md` through `sync`. Code-adjacent reading notes go to `src/**/README.md`.
