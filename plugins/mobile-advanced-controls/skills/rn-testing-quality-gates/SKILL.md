---
name: rn-testing-quality-gates
description: Execution-grade skill for React Native testing strategy and deterministic CI quality gates protecting critical user flows.
metadata:
  domain: mobile-advanced-controls
---

# Skill: rn-testing-quality-gates

## Purpose
Define high-signal testing architecture and CI quality gates that reduce flakiness and protect critical product behavior.

## When to Use
- Designing test strategy for unit/integration coverage.
- Defining CI merge gates and failure triage rules.
- Reducing flaky test behavior in RN projects.

## When Not to Use
- One-off manual QA instructions with no automated test scope.
- Non-RN runtime issues unrelated to test architecture.

## Required Inputs
- Critical user flows and risk priorities.
- Current test stack and CI pipeline constraints.
- Existing flaky test patterns.
- Required coverage and quality gate thresholds.
- Mocking/network/time control requirements.

## Framework-Specific Directives
- Jest + RNTL:
  - test behavior contracts, not implementation internals.
- CI quality gates:
  - gate merges on critical-path failures.
- Mocking strategy:
  - keep mock surface minimal and deterministic.

## Technical Implementation Patterns
- Build risk-based test priority matrix.
- Define clear unit vs integration boundaries.
- Standardize deterministic fixture and time/network controls.
- Enforce gate policies by test criticality.

## Anti-Patterns
- Over-mocking that hides real behavior failures.
- Flaky time/network-dependent tests without controls.
- Treating all tests with equal gate severity.

## Decision Tree
- If flow is business-critical:
  - enforce integration coverage and strict CI gates.
- If flow is low-risk utility behavior:
  - prioritize focused unit coverage.
- If flake rate is high:
  - stabilize test determinism before expanding suite.

## Execution Workflow
1. Collect required inputs.
2. Build risk-based coverage matrix.
3. Define test boundaries and deterministic fixtures.
4. Implement/adjust CI quality gate thresholds.
5. Add flake detection and triage rules.
6. Validate reliability of critical-path tests.
7. Produce structured output.

## Edge Cases
- Tests pass locally but fail in CI due to timing/environment drift.
- Critical flow untested because ownership boundary is unclear.
- Mock behavior diverges from production API contract.
- Flake masking hides genuine regression.

## Observability
- Track pass/fail trends by suite and risk class.
- Track flake rate and retry-triggered recoveries.
- Track quality gate failure causes over time.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Risk-priority matrix is explicit and actionable.
- Critical-path coverage and CI gates are enforced.
- Test determinism controls are in place.
- Flake triage workflow is defined.

## Risks / Rollback
- Risk: strict gates block velocity without signal gain.
  - Rollback: rebalance gate severity by risk class.
- Risk: weak gates allow regressions into production.
  - Rollback: tighten critical-path thresholds and add missing integration coverage.
