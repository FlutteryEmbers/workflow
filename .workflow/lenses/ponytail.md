# Lens: Ponytail

Use this lens when the user explicitly wants a demo-first proof of concept: the
smallest repeatable happy path under controlled inputs, not a production-ready
miniature.

This lens reduces speculative fields, validation, abstractions, dependencies,
and compatibility work. It does not reduce the need to understand the real
flow before choosing the smallest solution.

## Design Reference

The necessity ladder, anti-scaffolding posture, and explicit upgrade-trigger
idea are adapted from the MIT-licensed
[Ponytail skill](https://github.com/DietrichGebert/ponytail/blob/main/skills/ponytail/SKILL.md).
Workflow Lite keeps its own task, mode, write, review, and artifact boundaries.

## Demo Contract

- `Demo Path`: the one end-to-end path that must work repeatedly.
- `Controlled Inputs`: fixtures, local inputs, single-user assumptions, or
  other conditions the demo may rely on.
- `Relaxed Validation`: production-grade checks intentionally omitted.
- `Real-World Safety`: protection retained for real credentials, paid or
  production side effects, irreversible writes, and demo credibility.
- `Deferred`: fields, abstractions, compatibility, validation, and operations
  work intentionally left out.
- `Trigger To Add`: observable evidence that justifies adding a deferred item.

## Necessity Ladder

Stop at the first sufficient rung after reading the task, relevant code, and
the real path end to end:

1. Does this need to exist to demonstrate the current PoC claim? If not, omit
   it and name the trigger that would justify it later.
2. Does the repository already provide a suitable helper, type, pattern, or
   path? Reuse it.
3. Can the standard library or native platform provide the required behavior?
   Use it.
4. Can an already-installed dependency provide the behavior without a new
   abstraction? Use it.
5. Only then add the minimum custom structure needed for the demo path.

The smallest change in the wrong place is not a valid Ponytail result. Bug
fixes still target the shared root cause when that is the smaller complete fix.

## Design Gates

- Every field needs a current consumer on the demo path.
- Every validation rule needs a current failure that would invalidate the demo,
  obscure a supported-input error, or create a real-world risk.
- Every abstraction needs a current boundary requirement. Do not add an
  interface for one implementation, a factory for one product, configuration
  for a value that does not vary, or an extension point without a current use.
- Every dependency needs an observed gap that existing code, the standard
  library, native features, and installed dependencies do not cover.
- “Might be needed later” is not sufficient evidence. Put it in `Deferred`
  with `Trigger To Add` instead of scaffolding it now.
- Prefer fewer files, deletion, and boring direct code when they still preserve
  the real flow and the demo claim.

## Demo-First Validation

For controlled, disposable, local demo inputs, validation may be deliberately
relaxed. Support the declared happy path, fail fast when a required demo input
is absent, and do not promise stable behavior for unsupported inputs.

Default to omitting:

- exhaustive schema or field validation;
- unknown-field rejection and normalization for unsupported inputs;
- schema migration, backward compatibility, and version negotiation;
- complete error taxonomies, recovery paths, and validation hooks;
- defensive checks repeated across trusted internal calls.

Do not add `schema_version` unless at least one current condition applies:

- more than one schema version must be distinguished now;
- current data must survive across versions;
- independent current readers and writers must negotiate the format; or
- versioning or migration is itself part of the demo claim.

## Safety Floor

Do not relax protection against:

- real credential or secret exposure;
- unintended paid, production, or externally visible side effects;
- irreversible loss of non-disposable data;
- a failure that would make the demo result misleading; or
- behavior the user explicitly requires.

Use the minimum protection that covers the real risk. Do not expand a local,
controlled demo boundary into an imagined production trust model.

## Task Effects

### shape + ponytail

Keep the output concept-level. Use existing shape fields to identify the demo
path, controlled assumptions, required-now behavior, rejected larger scope,
deferred work, and upgrade triggers. Do not produce target files, ordered
implementation steps, or step-level verification.

### plan + ponytail

Encode the Demo Contract into the existing plan scope, constraints,
verification, stop conditions, and notes. Name validation that may be relaxed,
fields or abstractions that must not be added, the minimum credible demo check,
and triggers for later productionization. A handoff must be explicit enough
that build or an external agent does not need to make new product or
architecture decisions.

### review + ponytail

Judge whether each field, validation rule, dependency, abstraction, and
compatibility mechanism has current evidence. Treat speculative complexity as
a finding when it exceeds the Demo Contract. Return the smallest repair
direction; do not rewrite the target or create an implementation plan.

## Output Hints

When useful, organize Ponytail-specific material as:

```text
Ponytail
- Demo Path:
- Controlled Inputs:
- Required Now:
- Relaxed Validation:
- Do Not Add:
- Real-World Safety:
- Deferred:
- Trigger To Add:
- Minimum Proof:
```

This is a lens output aid, not a new persisted artifact schema. Map it into the
selected task's existing output and template fields.

## Boundaries

- Activate only when the user explicitly selects `Lens: ponytail`.
- Do not infer it from `PoC`, `demo`, `YAGNI`, or over-engineering language;
  `route` may recommend it but must not apply it.
- There is one posture only. Do not add intensity levels or session-persistent
  state.
- The lens does not change task responsibility, write permission, sync
  permission, review authority, build authorization, or execution scope.
- The lens does not automatically select `Compatibility: breaking` or
  `Constraint Mode: prototype_exception`; those policies still require
  explicit user or source intent.
- `build` does not select this lens. It executes Ponytail constraints only when
  an explicit plan has already encoded them.
