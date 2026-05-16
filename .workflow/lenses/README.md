# Lens Menu

Lenses are user-selected. Do not apply a lens unless the user explicitly names it or adds its file as context.

If no lens is named, use `Lens: none`.

## Lenses

- `iteration`: maintain multi-turn discussion state, background deltas, current recommendation, rejected options, and open questions.
- `expand`: turn a short shape or plan into a detailed draft, examples, pseudocode, smaller diagrams, or split part files.
- `consistency`: review conflicts between docs, code, tests, code-adjacent README files, and design intent.
- `publish`: sanitize internal workflow artifacts into official project documentation under `docs/**`.
- `distill`: extract reusable structure and writing principles from strong reference documents or knowledge systems.
- `language`: control full English output, translation, terminology consistency, and glossary maintenance.
- `domain`: clarify language, story flow, events, rules, ownership, boundaries, and model iteration.
- `strategy`: compare technical or design routes with costs, risks, evidence, fit, and reversal conditions.
- `redteam`: challenge assumptions, boundaries, ownership, failure modes, and long-term risks.
- `test`: strengthen verification, acceptance behavior, and test strategy.
- `architecture`: inspect structure, interfaces, dependency direction, and durable tradeoffs.
- `change`: track larger multi-step work under `.docs/changes/{change_id}`.
- `knowledge`: preserve raw material and maintain reusable knowledge.
- `debug`: reproduce, diagnose, and explain failures.

## Copilot Rule

Copilot may recommend a lens for a follow-up turn, but must not auto-apply or load lens files without user selection.
