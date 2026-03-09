---
name: rn-testing-quality-gates
description: Testing strategy for React Native projects with Jest and React Native Testing Library. Use for test architecture, mocking strategy, integration coverage, CI quality gates, and regression prevention for critical user flows.
---

# RN Testing and Quality Gates

## Mission
Design high-signal test suites that protect business-critical behavior with minimal flakiness.

## Testing Workflow
1. Prioritize test coverage by product risk.
2. Define unit vs integration boundaries.
3. Build deterministic test data/mocks.
4. Configure CI gates and failure triage rules.

## Quality Rules
- Test behavior, not implementation details.
- Keep mock surface minimal and realistic.
- Isolate time/network randomness.
- Gate merges on critical-path failures.

## Coverage Priorities
- Authentication and session integrity.
- Checkout/payment or equivalent core revenue paths.
- Offline/online transition behavior.
- Error-state and retry flows.

## Output Contract
Use sections in this order:
- Test Strategy
- Priority Matrix
- Sample Tests
- CI Gates
- Flake Reduction Plan

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
