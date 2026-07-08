# Review

{{CONTENT: /.workflow/templates/_persist_metadata.md}}

## Language / Style

{{default: Chinese explanations with English technical terms preserved; use full English only when requested}}

## Review Target

{{code, docs, session decision, external plan, diff, artifact, or behavior claim}}

## Review Target Kind

{{plan | diff | code | docs | decision | claim | artifact}}

## Intended Next Use

{{discussion | persist | build | external-agent | sync}}

## Review Type

{{verdict-review | gap-analysis | diff-review}}

## Review Question

{{what this review is trying to decide}}

## Baseline

{{expected state, documented promise, user goal, workflow scenario, external plan, or none}}

## Source Context

- {{plan, diff, code path, project doc, session artifact, or user claim}}

## Discussion Notes To Preserve

{{preserve discussion details that clarify the review question, user concern, evidence priority, accepted risk, or why the verdict changed. Do not redesign or rewrite the target here, and do not preserve full transcript or conversational noise.}}

## Evidence Checked

- {{file, diff, command output, doc, artifact, or discussion evidence}}

## Decision-Relevant Facts

- {{fact that materially changes the verdict}}

## Assumptions vs Facts

- Fact: {{confirmed input}}
- Assumption: {{inference that still needs validation}}

## Discussion Trace

- Trigger: {{why this review exists}}
- Context Added: {{background that changed the verdict}}
- Decision Trail: {{initial concern -> evidence -> verdict}}
- Rejected Options: {{fixes or interpretations rejected}}
- Open Questions: {{remaining uncertainty}}

## Decision Trail

{{how evidence changed or confirmed the verdict}}

## Review Verdict

{{ready | needs changes | needs more evidence | blocked | docs blocked}}

## Plan Review Rubric

{{only when Review Target Kind is plan}}

- Intended Use Fit: {{whether the source input and plan detail support Intended Next Use}}
- Scope / Target: {{clear | unclear; evidence}}
- Allowed Changes / Do Not Touch: {{clear | unclear | not-applicable; evidence}}
- Compatibility / Constraint Policy: {{clear | unclear | not-applicable; evidence}}
- Repo Evidence: {{sufficient | insufficient | not-applicable; evidence}}
- Sequencing: {{coherent | needs changes; evidence}}
- Verification: {{sufficient | insufficient; evidence}}
- Stop Conditions: {{clear | unclear; evidence}}
- Authorization Boundary: {{does not imply unauthorized write/build/sync | problematic; evidence}}

## Readiness

- Review Type: {{verdict-review | gap-analysis | diff-review}}
- Confidence: {{high | medium | low}}
- Readiness: {{0-10}}
- Blocking Gaps: {{must-fix before intended next use, or none}}
- Non-blocking Gaps: {{can track without blocking, or none}}
- Recommended Action: {{none | persist | sync project-docs | sync session-archive | shape | plan | build | external-agent}}
- Can Promote Source: {{yes/no}}
- Can Use For Intended Next Use: {{yes | no | not-applicable}}

## Findings

| Severity | Finding | Evidence | Recommended Action |
| :--- | :--- | :--- | :--- |
| {{severity}} | {{finding}} | {{evidence}} | {{action}} |

## Gap Analysis

{{required for Review Type: gap-analysis; optional otherwise}}

| Gap | Severity | Evidence | Impact | Why It Matters | Recommended Action | Recommended Next Task |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| {{missing capability, behavior, evidence, or alignment}} | {{high | medium | low}} | {{observed evidence}} | {{affected scenario, user, workflow, or handoff}} | {{why this should or should not be solved now}} | {{repair direction or none}} | {{shape | explore | plan | sync | build | external-agent | persist | none}} |

Severity rules:

- `high`: blocks next write, build, sync, source-of-truth decision, intended plan use, or core workflow scenario.
- `medium`: does not block immediately but creates material rework, ambiguity, drift, user friction, or maintenance risk.
- `low`: clarity, polish, convenience, or non-blocking completeness issue.

## What Is Still Reasonable

- {{part of the target that can remain unchanged}}

## Required Revisions

- {{required change before intended next use}}

## Repair Direction

- {{minimal direction of change, not a full redesign or plan rewrite}}

## Open Questions

- {{ordinary review uncertainty}}

## Failure Or Risk Path

> Optional. Keep this diagram only if it makes the finding easier to understand.

```mermaid
flowchart TD
    trigger_node["Trigger"] --> risk_node["Risk"]
    risk_node --> impact_node["Impact"]
```

## Project Docs Rules Check

{{only when docs/** is involved}}

- Source clear: {{yes/no}}
- Scope clear: {{yes/no}}
- Docs type allowed: {{yes/no}}
- Source of truth clear: {{yes/no}}
- Alignment success criteria clear: {{yes/no}}
- Existing docs tone and structure preserved: {{yes/no}}
- Session-only material excluded: {{yes/no}}

## Docs Follow-up

- Impact: {{none | suggested | required}}
- Target: {{allowed docs/** path or none}}
- Reason: {{what code/docs alignment mistake could happen without docs update}}
- Recommended Sync: {{sync prompt or none}}

## Verification

- {{verification performed or needed}}

## Follow-up

- {{persist, sync, shape, plan, build, external-agent, or none}}

## Recommended Next Task

{{shape | plan | build | sync | persist | external-agent | none}}

## Next Use

{{persist | plan | build with explicit invocation | external-agent | sync | none}}
