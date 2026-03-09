---
name: mobile-payments-subscriptions
description: Payments and subscription system guidance for mobile apps. Use for purchase flows, entitlement state modeling, subscription lifecycle handling, receipt validation, grace periods, and billing failure recovery.
---

# Mobile Payments and Subscriptions

## Mission
Build revenue-critical purchase systems that remain consistent under failure.

## Workflow
1. Define entitlement source-of-truth and reconciliation path.
2. Implement purchase, restore, upgrade/downgrade flow.
3. Handle renewals, grace periods, and billing retry states.
4. Provide fraud/abuse controls and auditability.

## Output Contract
- Revenue Flow Model
- Entitlement State Machine
- Failure Handling
- Compliance Notes
- Test Matrix

## Senior Execution Mode
- Start by identifying system boundaries, assumptions, and risk level.
- Prefer smallest safe change that can be validated quickly.
- Keep recommendations production-focused: reliability, maintainability, and operational clarity.
- Make platform differences explicit when behavior diverges between iOS and Android.

## Decision Heuristics
- Prefer deterministic and testable architectures over clever shortcuts.
- Choose explicit typed contracts for all module boundaries.
- Reject ambiguous state ownership; define single source of truth.
- Prioritize debuggability and rollback safety for release-impacting changes.

## Code Quality Gates
- Enforce strict TypeScript (no implicit any, typed inputs/outputs).
- Avoid hidden side effects and broad mutable shared state.
- Keep components/services single-purpose and composable.
- Prevent unnecessary re-renders by controlling subscription and prop surfaces.

## Review Checklist
- Correctness: Does the solution handle edge and failure states?
- Scale: Does it remain maintainable as features grow?
- Performance: Are hot paths optimized with measurable intent?
- Operations: Can this be monitored, debugged, and rolled back safely?

## Response Style
Always provide:
- clear problem framing,
- actionable implementation,
- verification steps,
- one senior-level follow-up recommendation.
