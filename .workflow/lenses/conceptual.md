# Lens: Conceptual

Use this lens when the user wants concept-first planning, high-level planning, or a staged handoff from a stronger model to a weaker model or execution agent.

## Add Only When Needed

- The user explicitly asks for `conceptual`, `concept`, `high-level`, or `low-level implementation plan`.
- The user wants to avoid premature code-level detail.
- The user wants a strong model to produce direction and a weaker model or OpenCode to refine or execute.
- The user needs to separate concept, high-level plan, and implementation plan artifacts.

## Planning Levels

- `concept`: goal, principles, boundaries, key tradeoffs, success criteria, and non-goals. Do not include file-level implementation steps.
- `high-level-plan`: phases, work packages, dependencies, validation direction, sequencing options, and risks. Do not treat this as direct build input.
- `implementation-plan`: target files, allowed changes, do-not-touch areas, step -> verify, stop conditions, and handoff notes for weak-model or OpenCode execution.

## Checks

- State the current `Planning Level`.
- Keep concept output conceptual; do not drift into executable steps.
- Keep high-level plans focused on phases and dependencies; do not over-specify patches.
- Include implementation details only when the user asks for an implementation plan.
- If a high-level plan is being handed to `build`, recommend first converting it into an implementation plan.

## Relationship To Strategy

- `strategy` compares routes or options.
- `conceptual` controls the planning level.
- If both are selected, use `strategy` for option comparison and `conceptual` for level boundaries.

## Output Hints

Add this field near the top of the answer when the lens is selected:

```text
Planning Level: concept | high-level-plan | implementation-plan
```

Only `implementation-plan` is intended as a direct execution handoff. This is a planning convention, not a write permission rule.
