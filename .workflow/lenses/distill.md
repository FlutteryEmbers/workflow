# Lens: Distill

Use this lens when a strong reference document, architecture folder, RFC, ADR, handbook, or knowledge base should be studied for reusable structure and writing principles.

## Add Only When Needed

- The user has a reference artifact that feels good, but the reason is unclear.
- The goal is to learn structure, reader journey, information layering, or reusable documentation principles.
- The user wants to improve Workflow Lite outputs based on external examples.
- The work should separate source context from ideas that can transfer to this repo.

## Do Not Use For

- Ordinary source preservation; use `knowledge`.
- Translation or style normalization; use `language`.
- Choosing between implementation routes; use `strategy`.
- Automatically changing `.workflow/**`; use `shape -> plan -> build` after distillation.

## Checks

- Observe structure before evaluating quality.
- Do not treat source content summary as distillation.
- Identify the reader journey: what question the reader starts with and how the document leads them to an answer.
- Identify information types such as context, decision, evidence, ownership, lifecycle, examples, risks, and follow-ups.
- Explain why the structure works: reduced cognitive load, strong navigation, clear source of truth, traceable decisions, or stable terminology.
- Separate transferable principles from source-specific context.
- Call out adoption cost and whether the idea would make Workflow Lite heavier.
- End with an adoption recommendation: `adopt`, `adapt`, `reject`, or `revisit`.

## Output Hints

- Use `.workflow/templates/distillation.md`.
- Exploration output: `.docs/work/notes/note_{source}_distillation.md`.
- Shaping output: `.docs/work/shapes/shape_distill_{topic}.md`.
- Durable knowledge should be synced to `.docs/knowledge/wiki/**` or `.docs/shared/**`.
- Do not write distillation results directly into `.workflow/**`.
