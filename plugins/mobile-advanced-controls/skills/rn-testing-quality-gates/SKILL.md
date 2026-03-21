---
name: rn-testing-quality-gates
description: Execution-grade skill for designing React Native testing layers, CI quality gates, flake control, and release-confidence thresholds.
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
- Target platform: `ios` | `android` | `both`.
- App size: `small` | `medium` | `large`.
- Release sensitivity: `normal` | `high-risk` | `release-blocking`.
- Current test stack/tools (e.g., Jest, RNTL, Detox, Maestro, contract tests).
- CI environment constraints (runtime limits, parallelism, device/emulator availability).
- Critical user journeys and business-risk areas.
- Feature volatility and churn hotspots.
- Existing flake history and reliability issues.
- Module boundaries needing contract tests.
- E2E coverage expectations.
- Merge gate expectations.
- Release gate expectations.
- Context: `greenfield` | `legacy stabilization`.

## Framework-Specific Directives
- Jest + RNTL:
  - define explicit ownership between unit and integration tests.
  - test behavior contracts, not implementation internals.
- Detox/Maestro (when used):
  - focus UI/E2E coverage on critical user journeys only.
  - do not use E2E as substitute for missing integration architecture.
- Contract/API boundary tests:
  - validate module interfaces where cross-module breakage risk is high.
- CI quality gates:
  - design merge/release gates around confidence signals, not raw test counts.
  - enforce deterministic gate ownership per layer and risk level.
- Flake control:
  - treat flake reduction as first-class; unstable suites require triage policy.
- Mocking strategy:
  - keep mock surface minimal and deterministic.
- Snapshot usage:
  - keep snapshots structural; do not treat snapshots as primary behavior correctness signal.

## Technical Implementation Patterns
- Testing layer ownership map:
  - assign responsibilities for unit, integration, and E2E layers.
- Critical path coverage map:
  - map high-risk journeys to minimum required test layers.
- Merge-blocking gate policy:
  - define blocking suites/checks for pull-request confidence.
  - prioritize fast feedback and developer velocity.
- Release-blocking gate policy:
  - define stricter gates for production readiness.
  - prioritize maximum confidence and stricter thresholds.
- Flake quarantine policy:
  - isolate unstable tests from hard gates with explicit remediation ownership.
  - require explicit ownership and SLA for fixing quarantined tests.
  - quarantine must not become permanent; enforce revalidation before re-entry into blocking gates.
- Retry policy boundaries:
  - allow limited retries only for known transient failures (e.g., network, device startup).
  - do NOT retry deterministic logic failures.
  - track retry rate as a signal of hidden flakiness.
- Deterministic fixture strategy:
  - standardize time/network/randomness controls across suites.
  - enforce controlled time, randomness, and network responses.
  - avoid reliance on real-time clocks or external services in tests.
- Test environment parity:
  - ensure CI environment closely matches local runtime (timing, device config, network conditions).
  - avoid hidden dependencies on local-only configurations.
- Test data ownership rules:
  - define source and lifecycle for fixtures per module boundary.
- Contract testing between modules:
  - enforce interface compatibility at integration seams.
- Snapshot boundary rules:
  - restrict snapshots to stable UI structure surfaces.
- Confidence threshold matrix:
  - define required pass reliability by layer, risk class, and release phase.

## Anti-Patterns
- Using E2E tests to compensate for missing integration tests.
- Blocking releases on unstable/flaky suites without triage policy.
- Duplicating the same scenario across all test layers.
- Overusing snapshots for behavior validation.
- Allowing non-deterministic fixtures/timers/network conditions.
- Running broad UI suites without critical-path targeting.
- Treating test count as quality.
- Over-mocking that hides real behavior failures.

## Decision Tree
- If scenario is logic-local and deterministic:
  - assign to unit ownership.
- If scenario crosses modules/state boundaries:
  - assign to integration ownership.
- If scenario is end-user critical journey:
  - assign targeted E2E ownership.
- If gate is merge-time and confidence is moderate:
  - use merge-blocking checks plus warning-only non-critical suites.
- If gate is release-time or high-risk:
  - enforce stricter release-blocking thresholds.
- If suite is historically flaky:
  - quarantine from hard gates until stabilized.
- If validation need is visual structure only:
  - snapshot may be used.
- If validation need is interaction/behavior:
  - require integration or E2E interaction tests.
- If context is greenfield:
  - define layer ownership and thresholds from start.
- If context is legacy:
  - stabilize flake and prioritize critical-path gaps before broad expansion.

## Execution Workflow
1. Collect required inputs.
2. Classify risk level and critical user journeys.
3. Define testing layer responsibilities.
4. Define merge and release quality gates.
5. Define flake control and retry policy.
6. Define fixture/data determinism rules.
7. Define confidence thresholds per layer.
8. Validate CI cost vs confidence tradeoffs.
   - remove low-signal, high-cost tests.
   - prioritize tests that protect critical user journeys.
9. Produce structured output.

## Edge Cases
- Tests pass locally but fail in CI due to timing/environment drift.
- E2E passes while integration layer misses regression.
- Snapshots pass while behavior is broken.
- Platform-specific issue appears only on iOS or Android.
- Retry policy masks real regressions.
- Retries hide real regression, causing false green CI results.
- Release blocked by historically flaky suite.
- Critical path has low coverage while non-critical coverage is high.
- Async UI timing causes false failures.
- Mocked tests diverge from runtime behavior.
- Legacy suite too unstable for strict release gates.

## Observability
- Track flake rate by suite and layer.
- Track merge-blocking vs warning-only failure trends.
- Track retry frequency per suite as a leading indicator of instability.
- Detect repeated regressions in critical user paths.
- Record test duration and CI bottlenecks.
- Monitor release gate failures over time.
- Surface confidence gaps between unit/integration/E2E layers.
- Track quality gate failure causes over time.
- Do not log sensitive user data or secrets.

## Output Contract
- Context Summary
- Assumptions
- Risk / Critical Path Classification
- Testing Layer Ownership
- Merge / Release Quality Gates
- Flake Control Strategy
- Confidence Threshold Matrix
- Verification Matrix
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Risk and critical-path classification is explicit and actionable.
- Unit/integration/E2E ownership boundaries are defined.
- Merge and release gate policies are deterministic.
- Test determinism controls are enforced.
- Flake triage/quarantine workflow is defined.
- Confidence thresholds are defined per layer and release sensitivity.
- Critical-path regression detection is covered.

## Risks / Rollback
- Risk: strict gates block velocity without signal gain.
  - Rollback: rebalance gate severity by risk class.
- Risk: weak gates allow regressions into production.
  - Rollback: tighten critical-path thresholds and add missing integration coverage.
