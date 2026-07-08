# Lens: Boundary

Use this lens when ownership, dependency direction, contract leakage, provider boundaries, adapter boundaries, package integration, or migration ownership matters.

This lens is not a task. It strengthens the selected task without changing write, sync, execute, source-of-truth, review, or build authority.

## Focus

- Ownership: which layer owns a behavior, decision, policy, or capability.
- Dependency direction: which layer may import, call, or know about another layer.
- Contract leakage: whether a contract mirrors provider, package, or implementation internals instead of system needs.
- Provider-local business: capability-local behavior that belongs inside a provider.
- Main-system business: product, workflow, lifecycle, permission, source-of-truth, state, or cross-provider policy that must stay out of a provider.
- Migration path: whether provider behavior can later move back into the package without unplanned system contract churn.

## Boundary vs Consistency

Use `boundary` for ownership and dependency direction.

Use `consistency` for source-of-truth, docs/code drift, contract/implementation alignment, session artifact drift, and stable-document projection readiness.

When both matter, combine lenses explicitly:

```text
Lens: boundary, consistency
```

Do not create a mixed lens for this. Organize output in lens order, then provide a converged result.

## Task Effects

### shape + boundary

Shape the boundary model before planning or implementation:

- `Boundary Model`: System, Contract, Provider, and Package responsibilities.
- `Business Ownership`: provider-owned capability business vs main-system business.
- `Allowed Coupling`: allowed dependency direction, allowed package-facing logic, and allowed provider-local policy.
- `Forbidden Coupling`: system importing provider/package internals, contract mirroring package internals unless explicitly accepted, and provider owning main-system business.
- `Migration Path`: behavior that may later move back to the package.

### explore + boundary

Collect evidence without verdicts:

- `Boundary Evidence`: imports, call direction, contract terms, provider logic inventory, package API dependency, and package internal dependency.
- `Suspected Leakage`: observed-evidence suspicion only; do not label it wrong or blocking.
- `Missing Evidence`: boundary facts not found or not checked.
- Candidate review targets when a verdict is needed.

### review + boundary

Judge whether the boundary holds:

```text
Boundary Findings:
| Boundary Item | Current Location | Proper Owner | Evidence | Risk | Verdict | Recommended Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
```

Check that provider can contain provider-owned capability business but not main-system business. Check that future migration back to the package does not force unplanned system contract churn.

### plan + boundary

Turn boundary decisions or review findings into executable constraints:

- `Boundary Constraints`: allowed dependency direction, allowed provider-local business, forbidden main-system business in provider, and forbidden package/provider internals in system.
- `Contract Churn Policy`: when the contract may change and when provider/package must adapt.
- `Migration Path`: what can later move back to the package.
- `Stop Conditions`: stop if implementation requires unplanned system behavior, contract semantics, source-of-truth, ownership, or dependency-direction changes.

## Provider Boundary Rule

A provider may contain provider-owned capability business. It must not contain main-system business.

Use this distinction instead of the vague rule "provider cannot have business logic."
