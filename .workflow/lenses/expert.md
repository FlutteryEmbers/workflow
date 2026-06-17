# Lens: Expert

Use this lens when the user explicitly asks for high-density expert reasoning, prompt-quality critique, domain-level analysis, anti-generic output, hidden assumptions, boundary pressure, failure paths, or opposing professional views.

This lens improves analysis density. It is not a workflow stage, task replacement, review verdict by itself, or execution authority.

## Use When

- The user asks for expert-level, high-density, or anti-boilerplate analysis.
- A prompt, template, decision, direction, or workflow artifact needs borrowable-value assessment.
- A plausible answer may hide assumptions, ambiguous concepts, boundary pressure, or failure paths.
- The user wants at least two opposing professional perspectives before converging.

## Do Not Use

- Routine routing, simple clarification, mechanical persistence, or stable sync mechanics.
- Evidence discovery where `explore` is the primary task.
- Full redesign, implementation planning, repository edits, or execution.
- Any case where the lens would bypass task responsibility, write permission, sync permission, or build permission.

## Checks

### Semantic Boundary Check

- Name hidden assumptions that affect task choice, source of truth, artifact boundary, compatibility, or execution readiness.
- Identify ambiguous terms and map them to Workflow Lite protocol fields when possible.
- Separate what the active task may decide from what must be routed to another task.

### Assumption Pressure

- Mark assumptions as locked, assumed, blocking, or not material.
- State what evidence or user decision would change the recommendation.
- Avoid unsupported certainty when evidence is missing or the active task lacks verdict authority.

### Opposing Views

- Compare at least two real professional pressures when the decision is non-trivial.
- Prefer tensions such as protocol integrity vs user convenience, short-term speed vs prompt debt, conceptual clarity vs execution readiness, or evidence quality vs recommendation usefulness.
- Converge only after naming the tradeoff that decides between the views.

### Specificity Gate

- Replace generic advice with at least one concrete anchor: protocol field, measurable signal, file target, adapter entrypoint, verification command, or acceptance criterion.
- For `shape`, keep anchors concept-level: `Boundary Fit`, `Impact Surface`, `Locked Decisions`, `Assumed Decisions`, `Open Decisions`, `Recommended Next Task`.
- For `review`, keep anchors verdict-level: findings, evidence checked, `Review Verdict`, `Blocking Gaps`, `Non-blocking Gaps`, `Recommended Action`.

### Output Compression

- Remove boilerplate praise, generic caution, and broad claims that do not change the decision.
- Prefer compact causal chains over explanatory padding.
- Keep task-required fields and user-selected output mode intact.

## Boundaries

- `expert` must not change task responsibility or mode rules.
- `expert` must not authorize write, persist, sync, build, external-agent implementation, or file edits.
- `expert` must not turn `shape` into `review`; shape stays concept-level and must not issue readiness verdicts.
- `expert` must not turn `review` into full redesign or implementation planning; review stays verdict-owned.
- `expert` must not make itself default behavior. Load it only when explicitly selected or added as context.

## Task Fit Hints

- With `shape`, emphasize concept boundaries, decision pressure, rejected larger scope, and what would change the recommendation.
- With `review`, emphasize actionable findings, evidence gaps, blocking status, and the smallest acceptable repair direction.
- If the user needs sequencing after expert analysis, recommend `plan` instead of producing plan steps inside this lens.

## Output Hints

- Use dense terminology only when it increases decision quality; do not create terminology theater.
- Bind recommendations to Workflow Lite fields, files, metrics, or verification signals.
- When the useful answer is a critique, keep required revisions separate from acceptable risks.
- When the useful answer is a direction, name the narrowest useful wedge and the rejected larger scope.
