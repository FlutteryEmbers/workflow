# Copilot Context Guide

Use this file as a manual Add Context menu. Prefer one mode, one task, selected lenses, and only the files needed for the current request.

## Mode Format

```text
Mode: <discuss|persist|execute>
Task: <route|clarify|explore|shape|plan|build|review|sync>
Lens: <none|iteration|expand|consistency|distill|language|domain|strategy|redteam|test|architecture|debug>
Target: <required for persist; otherwise none>
Plan: <required for execute; otherwise none>
Context:
- #.workflow/tasks/<task>.md
- #.workflow/lenses/<lens>.md only when selected
- #.workflow/templates/<template>.md only in Mode: persist
- #.session/goal/*, relevant .session/notes/**, relevant .session/decisions/**, docs/**, or source files
Request:
<what you want>
```

## Mode Rules

- `Mode: discuss` is the default. Add the task, selected lenses, and relevant context. Do not add templates. Do not create or update files.
- Ordinary `Mode: persist` writes a session artifact. Add the task, matching template, selected lenses, source context, and `.session/**` target.
- `Task: sync` in `Mode: persist` writes only `docs/**` or explicit `src/**/README.md`.
- `Mode: execute` applies an approved workflow-managed plan through `Task: build`.
- Native Codex/Copilot Plan -> Implement is the `external-agent` write path. It is not a Workflow Mode and does not use `Task: build`.

## Lens Selection Rules

- Default to `Lens: none`.
- Multiple lenses are allowed in `Mode: discuss` only when the user explicitly lists them.
- Copilot may recommend lenses, but must not load or apply them automatically.
- In multi-lens discuss, organize output in the user's lens order, then provide a converged recommendation and suggested persist step.
- In `Mode: persist`, prefer one primary lens and at most one supporting lens. If more lenses are needed, split into multiple persist steps.

## Understanding Check

- Start with an inline `## Understanding` unless the request is trivial.
- Keep it short: one sentence for simple work, up to three bullets for complex work.
- Include mode, task, selected lenses, target or plan, and key constraints when relevant.
- Do not ask for confirmation by default; continue in the same response.
- Ask before proceeding only when ambiguity would affect file writes, execution, scope, target, plan, or source of truth.

For `Mode: execute`, use `## Execution Understanding` and name the approved plan before changing files.

## Write Boundaries

- `discuss`: no writes.
- Ordinary `persist`: write `.session/**`.
- `sync` persist: write only `docs/**` or `src/**/README.md`.
- `execute`: may modify broader repository artifacts only when the approved plan says so.
- `external-agent`: native Plan/Implement may write files directly, but the native plan must be audited before implementation and the diff must be reviewed afterward.

## Task Boundary Check

Before acting, classify the request when it is not obviously a fit:

- `fits`: the current task can handle it directly.
- `fits_with_preflight`: the current task can handle it after a read-only preflight.
- `composite`: the request needs multiple tasks.
- `wrong_task`: another task is the proper entrypoint.
- `missing_prerequisite`: required target, approved plan, source of truth, or formal docs safety is missing.

If not `fits`, do not force-fit the request.

```text
Boundary: <fits|fits_with_preflight|composite|wrong_task|missing_prerequisite>
Reason: <one sentence>
Recommended Path: <task -> task>
Next Prompt: <copyable prompt>
```

## Preflight Vs Segments

Preflight is a same-response, read-only check. It is allowed only in `Mode: discuss`.

Allowed preflight:

- `shape`: inspect code/docs/session context before forming a direction.
- `plan`: perform target-to-repo fit before planning.
- `sync`: check Formal Docs Rules and consistency before recommending docs sync.
- `review`: read evidence before verdict.
- `build`: check approved-plan readiness only.

Preflight must not load templates, write files, run implementation, perform sync, or apply unselected deep lenses.

Segments are separate user-executed steps. Use segments for any write, approved plan, external-agent implementation, formal docs sync, or blocker resolution.

## Composite Task Segmentation

For composite requests, output segmented prompts:

```text
Recommended Segments:
1. <segment name>
   Mode:
   Task:
   Lens:
   Target/Plan:
   Context:
   Request:
   Expected Output:
   Continue Condition:
Stop Points:
- <approval, audit, source-of-truth, Formal Docs Rules, or diff review point>
```

Common segmentations:

- Judge current code/docs and decide what to do: `review -> shape/plan/sync/build`.
- Implement a feature from target docs and current code: `plan -> review -> external-agent/build -> review`.
- Move discussion into formal docs: `shape/plan/review -> sync`.
- Distill a reference and improve workflow: `explore --lens distill -> shape -> plan -> build`.

### Example: Judge Reasonableness, Then Decide Next Step

```text
Recommended Segments:
1. Review current fit
   Mode: discuss
   Task: review
   Lens: consistency, architecture
   Target/Plan: none
   Context: docs/**, relevant source files, related .session/decisions/**
   Request: Judge whether the current implementation is reasonable. Return verdict and evidence.
   Expected Output: reasonable | partially reasonable | unreasonable | unclear, with next task.
   Continue Condition: Continue only after the verdict is clear.
2. Shape or plan next step
   Mode: discuss
   Task: shape or plan
   Lens: strategy or architecture if selected
   Target/Plan: none
   Context: review verdict and source context
   Request: Form the next direction or implementation plan based on the review.
   Expected Output: recommendation or repo-aware plan.
   Continue Condition: Continue only after user accepts the direction or plan.
Stop Points:
- review verdict
- plan approval
```

### Example: Implement From Target Docs And Current Code

```text
Recommended Segments:
1. Plan from target to repo
   Mode: discuss
   Task: plan
   Lens: consistency, architecture, test
   Target/Plan: none
   Context: target docs, relevant source files, related .session/decisions/**
   Request: Produce target-to-repo fit, blockers, implementation sequence, and verification. Do not write files.
   Expected Output: repo-aware plan or blocker.
   Continue Condition: Continue only if there are no blockers.
2. Audit plan
   Mode: discuss
   Task: review
   Lens: redteam, test
   Target/Plan: none
   Context: plan and target docs
   Request: Audit the plan before implementation.
   Expected Output: approved | needs changes | blocked | docs blocked.
   Continue Condition: Continue only after approval.
3. Implement
   Mode: execute or external-agent path
   Task: build for workflow-managed execution
   Lens: test or debug if selected
   Target/Plan: approved plan
   Context: approved plan and target files
   Request: Implement only the approved plan.
   Expected Output: changes plus verification.
   Continue Condition: Continue only after implementation completes.
4. Review diff
   Mode: discuss
   Task: review
   Lens: consistency, test
   Target/Plan: approved plan
   Context: diff and approved plan
   Request: Review the diff against the approved plan.
   Expected Output: accept | revise diff | add verification | follow-up.
Stop Points:
- blocker in target-to-repo fit
- plan audit
- implementation completion
- diff review
```

## Formal Docs Rules

Any write to `docs/**` must name source, target audience, and source of truth. Exclude AI discussion residue, unconfirmed tradeoffs, rejected options, internal redteam-only risks, temporary workarounds, sensitive implementation detail, and not-yet-announced plans. If formal doc safety is unclear, output `docs blocked` and do not write `docs/**`.

## Discuss Context

Use this for brainstorming, comparison, critique, route guidance, code explanation, or early planning.

Add:

- #.workflow/tasks/<task>.md
- selected #.workflow/lenses/<lens>.md only when the user names it
- relevant `.session/goal/*`, `.session/notes/**`, `.session/decisions/**`, `docs/**`, or source files

Do not add:

- #.workflow/templates/**

## Persist Context

Use this when the user says save, write, generate, update, land, or persist a session artifact.

Allowed targets:

- `.session/notes/**` for `clarify` and `explore`
- `.session/decisions/**` for `shape`, `plan`, and `review`
- `.session/goal/**` for `shape`
- `docs/**` only for `Task: sync`
- `src/**/README.md` only for `Task: sync`

If `Target` is missing, propose a target path in chat instead of writing.

## Execute Context

Use this when the user asks to apply an approved workflow-managed plan.

Add:

- #.workflow/tasks/build.md
- approved plan or approved `.session/decisions/**` file
- target source files, docs, prompts, templates, or workflow artifacts named by the plan
- selected #.workflow/lenses/test.md or #.workflow/lenses/debug.md only when selected

If an approved plan is missing, do not modify files. Explain what plan is needed.

## External Agent Write Path

Use this when Codex/Copilot native Plan -> Implement should edit files directly. Workflow Lite is context, constraints, audit checklist, and diff review.

### 1. Discuss

```text
Mode: discuss
Task: shape
Lens: none or selected lenses
Request:
Discuss the target direction. Do not write files.
```

### 2. Native Plan

```text
Use native Plan phase. Create an implementation plan only; do not edit files.
Include goal, success criteria, target files, allowed changes, do-not-touch areas, step-by-step verification, risks, stop conditions, rollback, formal docs impact, and open questions.
```

### 3. Audit Plan

```text
Mode: discuss
Task: review
Lens: redteam, test, architecture
Request:
Audit this external plan before implementation. Return approved, needs changes, blocked, or docs blocked.
```

### 4. Native Implement

```text
Implement only the approved external plan. Use minimal diff. Do not broaden scope, perform drive-by refactors, or modify .session/**, .workflow/**, docs/**, or unrelated files unless explicitly listed in the approved plan. Stop and ask for plan/review if scope must expand.
After each major step, report the verification result requested by the plan.
```

### 5. Review Diff

```text
Mode: discuss
Task: review
Lens: consistency, test
Request:
Review the diff against the approved external plan. Identify scope drift, missing verification, Formal Docs Rules issues, and required follow-up.
Treat drive-by edits or unapproved scope expansion as blocking unless explicitly approved.
```

## Task To Template Map

- `clarify`: #.workflow/templates/note.md
- `explore`: #.workflow/templates/note.md; with `distill` use #.workflow/templates/distillation.md; with `strategy` use #.workflow/templates/options.md
- `shape`: #.workflow/templates/decision.md; for goal updates use #.workflow/templates/goal.md; optional #.workflow/templates/shape.md or #.workflow/templates/expanded.md
- `plan`: #.workflow/templates/decision.md or #.workflow/templates/plan.md; with `expand` use #.workflow/templates/expanded.md
- `review`: #.workflow/templates/decision.md or #.workflow/templates/review.md; with `redteam` use #.workflow/templates/critique.md; with `consistency` use #.workflow/templates/consistency_review.md
- `sync`: #.workflow/templates/sync.md; for `src/**/README.md` use #.workflow/templates/code_readme.md

## Common Scenarios

### Ask For Usage Guidance

```text
Mode: discuss
Task: route
Lens: none
```

Add #.workflow/tasks/route.md. Add #.workflow/copilot.md only when the user wants Copilot-specific Add Context guidance.

### Capture A Note

```text
Mode: persist
Task: clarify
Target: .session/notes/note_{topic}.md
```

Add #.workflow/tasks/clarify.md, #.workflow/templates/note.md, and relevant context.

### Persist A Decision

```text
Mode: persist
Task: shape
Target: .session/decisions/dec_{topic}.md
```

Add #.workflow/tasks/shape.md, #.workflow/templates/decision.md, selected lenses, and source context.

### Update Goal

```text
Mode: persist
Task: shape
Target: .session/goal/vision.md
```

Add #.workflow/tasks/shape.md, #.workflow/templates/goal.md, current goal context, and selected lenses.

### Persist A Plan

```text
Mode: persist
Task: plan
Target: .session/decisions/dec_{topic}_plan.md
```

Add #.workflow/tasks/plan.md, #.workflow/templates/plan.md or #.workflow/templates/decision.md, source decision, repo context, and selected lenses.

### Sync Formal Docs

```text
Mode: persist
Task: sync
Target: docs/{area}/{topic}.md
Source:
- .session/decisions/dec_{topic}.md
```

Add #.workflow/tasks/sync.md, #.workflow/templates/sync.md, relevant session decisions, existing docs, and source files. Apply Formal Docs Rules.

### Sync Code-Adjacent README

```text
Mode: persist
Task: sync
Target: src/{area}/README.md
```

Add #.workflow/tasks/sync.md and #.workflow/templates/code_readme.md.

### Execute A Plan

```text
Mode: execute
Task: build
Plan: .session/decisions/dec_{topic}_plan.md
```

Add #.workflow/tasks/build.md, the approved plan, and target artifacts named by the plan.

In chat, summarize decisions and next steps. Do not paste full artifact contents unless the user asks for a preview.
