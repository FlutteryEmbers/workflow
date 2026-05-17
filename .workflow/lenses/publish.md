# Lens: Publish

Use this lens when internal workflow artifacts should become sanitized official project documentation under `docs/**`.

## Add Only When Needed

- Stable content from `.docs/**` should be published for a formal project audience.
- The user explicitly asks to publish, sanitize, redact, or prepare official docs.
- A review should check whether internal notes are safe to expose.

## Required Inputs

- Source: the `.docs/**` artifact or other confirmed source material.
- Target: a `docs/**` path for official project documentation.
- Audience: the intended readers, such as engineering team, users, security reviewers, or onboarding readers.
- Source of truth: the confirmed fact source for the published claims.

## Publish Rules

- Do not copy `.docs/**` directly into `docs/**`.
- Preserve stable, confirmed facts that are useful to the target audience.
- Preserve the existing structure and tone when the target doc already exists.
- Remove AI discussion residue, prompt language, unresolved tradeoffs, rejected options, internal migration notes, temporary workarounds, sensitive implementation detail, security-sensitive detail, and unannounced plans.
- If the content needs owner, product, legal, or security review, block publication.

## Blockers

Output `Publish blocked` and do not write official docs when:

- Source is unclear.
- Target is not under `docs/**`.
- Audience is unclear.
- Source of truth is unclear.
- Sensitive or unresolved content cannot be safely separated.
- Required review is missing.

## Output Hints

- `review --lens publish`: identify publishable facts, do-not-publish content, blockers, and recommended action.
- `sync --lens publish`: workflow-managed fast path for publishing a sanitized official doc with `Mode: persist`, `Task: sync`, `Lens: publish`, `Target: docs/**`.
- External-agent publish: native Plan/Implement may publish `docs/**` only after the publish plan is audited and the resulting diff is reviewed against these rules.
- If the target does not exist, `.workflow/templates/publish_note.md` may be used as a light starting point.
