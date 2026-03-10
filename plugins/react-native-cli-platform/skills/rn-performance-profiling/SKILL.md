---
name: rn-performance-profiling
description: Execution-grade skill for React Native CLI performance profiling, bottleneck classification, and verified optimization delivery.
metadata:
  domain: react-native-cli-platform
---

# Skill: rn-performance-profiling

## Purpose
Identify measurable performance bottlenecks in RN apps and deliver verified, low-risk optimizations with before/after evidence.

## When to Use
- Investigating frame drops, startup latency, memory pressure, or slow renders.
- Profiling JS/native thread contention and bridge overhead.
- Validating optimization impact before rollout.

## When Not to Use
- Feature implementation with no profiling requirement.
- Performance claims without measurable baseline collection.

## Required Inputs
- Target platform/device matrix and build type.
- Performance symptom and affected screens/flows.
- Baseline metrics (FPS, startup time, memory, dropped frames).
- Profiling tooling and environment constraints.
- Regression threshold and acceptance criteria.

## Framework-Specific Directives
- RN CLI iOS:
  - Use iOS-native and RN-compatible profiling paths consistently.
- RN CLI Android:
  - Use Android-native and RN-compatible profiling paths consistently.
- Cross-platform:
  - Compare parity and avoid simulator-only conclusions for release decisions.

## Technical Implementation Patterns
- Capture baseline before changing code.
- Classify bottleneck domain: render, data, JS thread, native thread, memory.
- Apply targeted optimization per bottleneck class.
- Re-measure under same conditions and report delta.
- Keep optimization changes scoped and reversible.

## Anti-Patterns
- Optimizing before collecting baseline.
- Mixing profiling environments between baseline and verification.
- Applying broad refactors without measured bottleneck ownership.

## Decision Tree
- If no baseline exists:
  - collect baseline first.
- If bottleneck is render-path:
  - reduce rerenders and render-path cost first.
- If bottleneck is startup:
  - defer non-critical work and validate startup delta.
- If optimization impact is insignificant:
  - revert and choose next bottleneck target.

## Execution Workflow
1. Collect required inputs.
2. Capture reproducible baseline metrics.
3. Classify bottleneck domain and priority.
4. Define targeted optimization plan.
5. Implement scoped optimization changes.
6. Re-measure and compare against baseline.
7. Produce structured output.

## Edge Cases
- Simulator improvement does not reproduce on physical device.
- Fix improves one platform but regresses the other.
- Optimization reduces latency but increases memory pressure.
- Bridge optimization changes behavior under load.

## Observability
- Track baseline and post-change metrics per platform.
- Track regression events against performance thresholds.
- Track optimization rollback events when deltas are negative.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Baseline and post-change measurements use comparable conditions.
- Bottleneck classification matches observed metrics.
- Optimization impact is measurable and above acceptance threshold.
- Cross-platform regressions are checked.

## Risks / Rollback
- Risk: optimization introduces regressions outside measured path.
  - Rollback: revert scoped changes and restore last stable behavior.
- Risk: metric gains are not reproducible.
  - Rollback: discard optimization and reprofile with controlled setup.
