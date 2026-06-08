# Lens: Language

Use this lens for output language, terminology consistency, translation, readability, or project glossary updates.

## Default Style

Use Chinese explanations with English technical terms preserved.

Preserve task, lens, template names, code identifiers, file paths, API names, package names, CLI commands, front matter keys, and schema keys in English.

Use full English only when explicitly requested.

## Checks

- Are terms used consistently across session decisions, project docs, code, and APIs?
- Are near-synonyms intentionally different?
- Does the audience understand the explanation?
- Should a settled term be added to project docs?

## Output Hints

- Terminology notes can be persisted to `.session/inbox/**` or `.session/threads/{thread}/**`.
- Stable terminology choices enter `docs/reference/glossary.md` or another user-specified allowed docs target through `sync`.
- Stable terminology belongs in `docs/reference/glossary.md` or another user-specified allowed `docs/**` target through `sync`.
- Do not create language-specific directories or filename variants by default.
