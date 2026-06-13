# OpenCode Adapter

OpenCode support is a thin adapter. It does not replace `.workflow/tasks/**`, `.workflow/lenses/**`, or `.workflow/templates/**`, and it does not create a separate OpenCode workflow.

Use OpenCode when it helps with context reading, plan drafting, or bounded implementation. Keep `.workflow/**` as the source of truth for task semantics, lenses, write boundaries, and Project Docs Rules.

Output flow: use `Output: compact` for general discussion, `Output: normal` to refine before persist, and `Output: full` for artifacts, handoffs, audits, build-ready plans, or diff reviews.

For `Task: plan`, compact output must start from `Shape Summary` and a compact `Impact Surface` before the plan sketch. Use `Shape Summary: Source=chat` when there is no persisted shape artifact. Treat `Output: full` plan output as the detailed commitment artifact for persist, implementation handoff, build-ready planning, or external-agent handoff. `Depth: detailed` is persisted artifact metadata, not a chat output mode.

OpenCode may suggest an explicit redteam critique when the user asks for critique or an existing target has costly failure paths, but must not load or apply it automatically. Use full `redteam` only when the user explicitly selected that lens, asked for critique, or the prompt explicitly includes it.

Embedded critique is lightweight core behavior in `shape`, `plan`, and `build`; it names risks and stop conditions without loading the redteam lens or issuing review verdicts.

Use core `Abstraction Level` rules when the user explicitly wants concept-first planning, phase planning, low-level implementation planning, or strong-model-to-weak-model handoff.

When unsure, start with `shape`. Use `explore` for evidence and `review` for verdict. Lenses may strengthen the selected task, but must not change task responsibility.

## Discussion Freedom

Workflow Lite is human-in-the-loop first. In `Mode: discuss`, OpenCode may provide provisional thinking while the user keeps final responsibility.

- `shape` may output `Provisional Recommendation`, `Best Guess`, `Candidate Options`, and `What Would Change My Mind`.
- `explore` may output `Candidate Interpretations`, `Likely Entry Points`, and `Borrowable Ideas`.
- `review` may output `Minimal Revision Sketch` and `Repair Direction`.
- `plan` may output a non-build-ready `Planning Draft`.
- Include `Confidence`, `Assumptions`, and `Human Decision State` for uncertain or consequential output.
- In `shape`, `Human Decision State` is control flow, not tail metadata. Put it after current read and before recommendation. If state is `checkpoint`, use native user-input UI when available or output structured `User Checkpoint` and wait. If state is `blocking`, stop before final recommendation and `Persist Candidate`.

This does not loosen `/wf-build`, `persist`, `sync`, or external-agent write boundaries.

Default to `Compatibility: preserve` and `Constraint Mode: respect`. Breaking compatibility or constraint exceptions require explicit user or explicit-source intent.

Task shortcut:

- `shape = synthesis`: ambiguous, what-if, option-comparison, concept-level, direction-setting, or entrypoint-selection requests.
- `explore = evidence`: code/docs/reference/behavior/entrypoint/dependency fact gathering.
- `review = verdict`: existing target reasonableness, readiness, conflict, safety, or acceptance checks.
- `plan = planning sequence`: chosen direction to phases, repo-aware steps, or executable handoff.

Discovery vs judgment rule:

- Do not infer repo ownership or maintenance responsibility.
- Use `explore` for what exists, where it is, how it appears to work, and how reliable the evidence is.
- Use `review` for whether something is correct, acceptable, ready, worth changing, or which source should be treated as truth.
- Do not add the `consistency` lens for discovery questions.

Compatibility / constraint rule:

- `shape` may suggest breaking or override pressure, but cannot activate it automatically.
- `plan` must encode selected breaking or exception scope explicitly.
- `build` and bounded implement must stop on unplanned compatibility removal, alias removal, migration removal, fallback removal, or constraint bypass.
- `prototype_exception` is temporary PoC scope, not a durable project constraint.

Abstraction level rule:

- `shape` produces `Abstraction Level: concept`.
- `shape` records `Impact Surface` and `Recommended Next Abstraction Level` when it may feed planning.
- `plan` automatically chooses `Abstraction Level: phase-plan` or `implementation-plan` unless the user explicitly names one.
- `plan` inherits a shape artifact's `Recommended Next Abstraction Level` when present; without that, infer from the request and read-only preflight, defaulting to `phase-plan` when uncertain.
- `implementation-plan` is only for build-ready planning, external-agent handoff, weak-model handoff, or explicit execution preparation.
- Option comparison is built into `shape`; abstraction level is core protocol.
- `/wf-build` does not require an `Abstraction Level` label; it still requires an explicit executable plan.

Exploration notes:

- Explicit `notes/**` targets may be persisted as disposable exploration notes through `Task: persist`.
- Do not treat `notes/**` as project docs or implementation plans.
- Do not use OpenCode bounded implement from `notes/**`; promote useful conclusions into `.session/threads/**` or create an explicit external plan first.

## Usage Levels

### Level 1: Context Helper

Use this when OpenCode should summarize current state only.

- Read `.session/**`, `docs/**`, and relevant source files.
- Do not create a plan unless asked.
- Do not edit files.
- Return current state, relevant files, gaps, unknowns, and suggested next workflow task.

### Level 2: Draft Plan Generator

Use this when OpenCode native Plan can draft an implementation plan.

- Treat the result as a draft external plan.
- Do not edit files during native Plan.
- Audit the draft with `review` before implementation.
- Use `persist` to persist handoff plans to `.session/threads/{thread}/plan_{topic}.md`.

### Level 3: Bounded Implementer

Use this only after an explicit narrow plan exists.

- Implement only the explicit segment.
- Do not broaden scope.
- Do not touch `.session/**`, `.workflow/**`, `docs/**`, or unrelated files unless the explicit plan names them.
- Return compact `Execution Summary` by default; use full summary only for blocked, partial, failed verification, pitfalls, scope-expansion risk, or user-requested persistence.
- `Execution Summary` is factual, not a review verdict. Persist useful summaries through `persist` as `Artifact: note` and `Intent: audit`.
- Review the diff after implementation.

## Recommended Path

```text
OpenCode context summary
-> workflow review / shape / plan
-> Persist Packet -> persist thread handoff when useful
-> OpenCode bounded implement
-> workflow diff review
-> sync stable documents when settled
```

OpenCode native Plan belongs to the `external-agent` path. It is not `Mode: execute`. `Mode: execute` remains workflow-managed execution through `Task: build`.

## External Goal Intake

Use one of two paths:

- Long or reusable external source: persist `Artifact: brief` with `Brief Type: external-goal`, then shape from that inbox brief and persist the shape.
- Current conversational goal: shape directly in chat, then persist the shape if it is worth preserving.

`shape` is the reasoned projection from chat or an inbox goal brief. The durable shaped result belongs in `.session/threads/{thread}/shape_{topic}.md`.

## Slash Commands

OpenCode project commands live in `.opencode/commands/**`. They are thin prompt shortcuts, not a separate workflow. Keep task, lens, role, and template semantics in `.workflow/**`.

Default commands:

- `/wf`: route a request and recommend the smallest path.
- `/wf-clarify`: explain terms, prior answers, statements, assumptions, scope, and success criteria.
- `/wf-explore`: extract read-only evidence from code, docs, references, or unfamiliar repos.
- `/wf-shape`: discuss direction, what-if, option comparison, or concept design.
- `/wf-plan`: draft a repo-aware plan or external-agent handoff.
- `/wf-review`: review a plan, diff, code/docs claim, or readiness question.
- `/wf-persist`: persist `.session/inbox/**`, `.session/threads/**`, or explicit `notes/**`.
- `/wf-build`: execute an explicit plan only.
- `/wf-sync`: sync stable documents: `docs/**`, explicit `src/**/README.md`, or `.session/archive/<thread>/summary.md`.

Command rules:

- Commands default to `Output: compact` unless persistence, sync, audit, diff review, or handoff detail is required.
- Commands must not load all tasks or all lenses.
- `/wf`, `/wf-shape`, and `/wf-plan` may recommend `/wf-build`, but they must not execute implementation.
- `/wf-build` is a high-risk write command. It requires an explicit `Plan:` and may not use `notes/**` as an execution source.
- Calling `/wf-build` authorizes only the listed plan scope; it does not authorize scope expansion.
- `/wf-explore` uses the read-only `workflow-context` agent.
- `/wf-review` uses the read-only `workflow-review` agent.
- `/wf-sync` uses the permission-gated `workflow-docs-sync` agent.
- Do not add `AGENTS.md` for this adapter; use commands and `.workflow/opencode.md` explicitly.

Examples:

```text
/wf-clarify source of truth 在这个 workflow 里是什么意思？
/wf-explore 帮我看这个第三方 repo 是否有调用链入口
/wf-shape 这个 thread 命名规则应该怎么收敛？
/wf-review Audit this plan before implementation: <plan>
/wf-persist Artifact: shape Thread: workflow-thread-naming Topic: thread_inference Source: last discussion
/wf-build Plan: .session/threads/workflow-goal-removal/plan_goal_removal.md
/wf-sync Sync Object: architecture Source: .session/threads/workflow-goal-removal/decision_goal_boundary.md Scope: workflow memory Source Of Truth: session decision Target: docs/architecture.md Alignment Success Criteria: future workflow docs match confirmed boundaries
```

## Add Context

Use only the files needed for the current step.

- `.workflow/tasks/route.md` for choosing a path.
- `.workflow/tasks/clarify.md` for goal, scope, constraints, and success criteria.
- `.workflow/tasks/explore.md` for read-only evidence extraction.
- `.workflow/tasks/shape.md` for ambiguous, what-if, option-comparison, concept-level, or direction-setting work.
- `.workflow/tasks/review.md` for plan audit or diff review.
- `.workflow/tasks/plan.md` for a repo-aware handoff.
- `.workflow/tasks/persist.md` for session artifact persistence.
- `.workflow/tasks/build.md` for explicit-plan execution rules.
- `.workflow/tasks/sync.md` for project docs or code-adjacent README alignment.
- `.workflow/lenses/<lens>.md` only when explicitly selected.
- relevant `.session/inbox/**`, relevant `.session/threads/**`, `docs/**`, and source files as needed.
- explicit `notes/**` only when persisting disposable exploration notes.

Do not load all tasks or all lenses by default.

## Copyable Prompts

### Context Helper

```text
Use OpenCode as context helper only. Do not edit files and do not create an implementation plan.

Context:
- .session/inbox/<relevant>
- .session/threads/<relevant>
- docs/<relevant>
- <source files>

Request:
Summarize the current implementation state against the target context.

Return:
- Current state
- Relevant files
- Gaps
- Unknowns
- Suggested next workflow task
```

### Draft Plan

```text
Use native Plan phase only. Create a draft implementation plan and do not edit files.

Goal:
<goal>

Context:
- .session/inbox/<relevant>
- .session/threads/<relevant>
- docs/<relevant>
- <source files>

Plan requirements:
- Success criteria
- Compatibility: preserve | breaking
- Constraint Mode: respect | propose_override | prototype_exception
- Target files
- Allowed changes
- Do-not-touch areas
- Removed compatibility, migration/alias, and constraint exceptions when selected
- Assumptions
- Step-by-step verification
- Stop conditions
- Rollback or recovery
- Project docs impact
- Open questions
```

### Persist Thread Handoff

```text
Mode: persist
Task: persist
Artifact: plan
Artifact State: working | settled | superseded
Thread: <thread-name>
Intent: handoff
Depth: detailed
Topic: <topic>
Request:
Persist the OpenCode handoff plan. Preserve decision-relevant reasoning, not full transcript.
```

`persist` can apply explicit edits, but it must not invent a new direction, re-plan work, or turn unclear `needs changes` content into a settled artifact. Route those cases back to `shape`, `plan`, or `review`.

Use `persist shape_<topic>` to reference a shape by `Artifact ID`. This anchors source context and does not derive the thread directory. Thread directories are small closable work items; infer targets by same-work-item fit, reuse active threads only for the same work item, and include `Thread Inference Note` when assumptions matter.

### Persist Exploration Note

```text
Mode: persist
Task: persist
Artifact: note
Artifact State: inbox
Intent: exploration
Depth: compact | standard
Target: notes/<topic>.md
Request:
Persist this as a disposable exploration note. Do not treat it as project docs or an execution source.
```

### Plan Audit

```text
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Audit this OpenCode plan draft before implementation with explicit critique posture.

Return:
Review Verdict: ready | needs changes | needs more evidence | blocked | docs blocked

Check:
- Scope and target files
- Success criteria
- Step-by-step verification
- Source of truth
- Project Docs Rules if docs/** is touched
- Minimal diff risk
- Open questions
- Whether the plan exceeds the user's stated intent
```

### Bounded Implement

```text
Implement only the explicit external plan segment below. Do not broaden scope.
Use minimal diff. Do not perform drive-by refactors, formatting churn, unrelated cleanup, or opportunistic rewrites.

Explicit segment:
<explicit segment>

Do not modify .session/**, .workflow/**, docs/**, or unrelated files unless explicitly listed in the explicit segment.
Do not use notes/** as the explicit segment; notes are disposable exploration memory.
Stop and return to plan/review if implementation requires expanding scope, removing unplanned compatibility, or overriding unplanned constraints.

Report:
- Changed files
- Verification run for each major step
- Anything skipped and why
```

`/wf-build` is the command form of bounded implementation. Use it only when the explicit plan is present in `Plan:` or in the current prompt. If the plan is missing or not executable enough, `/wf-build` must return `missing_prerequisite` and avoid file edits.

`/wf-build` does not write `.session/**`. When execution pitfalls or blocked work should be remembered, persist the build output with `Task: persist`, `Artifact: note`, `Intent: audit`, and `Topic: <topic>_execution_summary`.

### Diff Review

```text
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the OpenCode diff against the explicit external plan.

Check:
- Scope drift
- Missing edits
- Unrelated edits
- Missing verification
- Drive-by refactors
- Project Docs Rules issues
- Required follow-up
```

### Stable Document Sync

```text
Use OpenCode sync only for stable-document projection.

Sync Domain:
<project-docs | session-archive>

Sync Object:
<architecture | feature | reference | code-readme | archive-summary | all>

Source:
- <session decision, diff, code, or existing docs>

Target Directory:
- <optional docs directory, src area, or archive directory>

Target:
- docs/<target>.md, src/<area>/README.md, or .session/archive/<thread>/summary.md

Scope:
<area or code/docs scope>

Source Of Truth:
<code | diff | session thread artifact | explicit user decision | existing docs>

Alignment Success Criteria:
<what code/docs alignment mistake this sync should prevent>

Rules:
- Follow Project Docs Rules.
- Source, scope, sync object, source of truth, alignment success criteria, and safe target selection must be clear.
- Use `Sync Object: architecture | feature | reference | code-readme | archive-summary | all`.
- Do not create workflow-internal docs such as docs/workflow/**, docs/session/**, docs/ai/**, docs/prompts/**, docs/notes/**, docs/plans/**, or docs/reviews/** unless the user explicitly declares host-project taxonomy override.
- Preserve existing docs structure and tone.
- For `session-archive`, require Source Thread, Thread Status, Archive Purpose, Summary Scope, Next Retrieval Use, and target .session/archive/<thread>/summary.md.
- Do not write .session/threads/**, .session/inbox/**, .workflow/**, source code, or unrelated docs.
- Output docs blocked if source, scope, sync object, source of truth, alignment success criteria, target selection, or safety is unclear.
- Output archive blocked if archive prerequisites are unclear.
```
