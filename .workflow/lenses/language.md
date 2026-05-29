# Lens: Language

Use this lens for output language, terminology consistency, translation, readability, or formal glossary updates.

## Default Style

Use Chinese explanations with English technical terms preserved.

Preserve task, lens, template names, code identifiers, file paths, API names, package names, CLI commands, front matter keys, and schema keys in English.

Use full English only when explicitly requested.

## Checks

- Are terms used consistently across session decisions, formal docs, code, and APIs?
- Are near-synonyms intentionally different?
- Does the audience understand the explanation?
- Should a stable term be added to formal docs?

## Output Hints

- Draft terminology notes can be saved to `.session/inbox/**` or `.session/drafts/**`.
- Accepted terminology choices can be saved to `.session/accepted/**` when accepted intent is explicit.
- Stable terminology belongs in `docs/glossary.md` or another user-specified `docs/**` target through `sync`.
- Do not create language-specific directories or filename variants by default.
