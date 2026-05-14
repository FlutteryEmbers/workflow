# Lens: Language

Use this lens when output language, translation, terminology consistency, or glossary maintenance needs explicit control.

## Default Style

- Write workflow artifacts with Chinese explanations by default.
- Preserve English technical terms when they are product names, workflow terms, code identifiers, file paths, API names, package names, CLI commands, front matter keys, or schema keys.
- Keep task, lens, template, directory, and file names in English.
- Do not create language-specific directories or filename suffixes by default.

## Full English Triggers

Use full English only when the user explicitly asks with wording such as:

- `用英文写`
- `full English`
- `English output`
- `rewrite in English`
- `Lens: language; Output Language: English`

## Checks

- Keep one stable spelling for each term.
- Prefer English when a Chinese translation would add ambiguity.
- On first use, optional format is `中文解释 (English Term)` when it improves readability.
- Do not translate code identifiers, paths, commands, API names, package names, schema keys, or front matter keys.
- Mermaid node IDs must stay simple English identifiers; node text may follow the requested output language.
- If stable terminology should be reused, suggest `sync --lens language` to update `.docs/shared/glossary.md`.

## Output Hints

- Default: Chinese explanations with English technical terms preserved.
- Full English: English prose with code/path/schema names unchanged.
- Glossary sync: write stable terms to `.docs/shared/glossary.md`.
