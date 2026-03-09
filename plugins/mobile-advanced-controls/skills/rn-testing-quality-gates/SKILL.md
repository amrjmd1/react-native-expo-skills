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
