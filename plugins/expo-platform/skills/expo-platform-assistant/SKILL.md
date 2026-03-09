---
name: expo-platform-assistant
description: Execution-grade skill for Expo platform architecture decisions, managed workflow boundaries, and deterministic implementation guidance across iOS and Android.
metadata:
  domain: expo-platform
---

# Skill: expo-platform-assistant

## Purpose
Provide deterministic Expo platform guidance for architecture, workflow boundary decisions, and implementation planning across managed, prebuild, and native escalation paths.

## When to Use
- Choosing managed vs prebuild vs bare boundaries.
- Planning Expo Router, config, and platform integration architecture.
- Debugging cross-platform Expo behavior with build/runtime constraints.
- Coordinating with specialized Expo skills when deeper handling is needed.

## When Not to Use
- Non-Expo projects.
- Pure backend architecture tasks with no mobile platform impact.

## Required Inputs
- Current workflow mode (managed/prebuild/bare).
- Expo SDK version and target platforms.
- Routing/navigation architecture.
- Config and environment strategy.
- Build and release constraints.
- Performance/reliability concerns and failure symptoms.

## Framework-Specific Directives
- Expo Managed:
  - Prefer Expo-first APIs and config-driven changes.
  - Avoid unnecessary native module escalation.
- Expo + EAS:
  - Keep environment and release inputs deterministic per profile.
  - Integrate build/release constraints in architecture decisions.
- Bare React Native:
  - Define clear boundary between Expo-managed and native-owned concerns.
  - Isolate native platform branches behind explicit abstractions.

## Technical Implementation Patterns
- Separate layers: UI, hooks, services, platform adapters.
- Keep feature boundaries explicit and composable.
- Use typed contracts for environment/config inputs.
- Route specialized concerns to focused skills with explicit handoff criteria.
- Minimize scope: smallest safe change first, then iterate.

## Anti-Patterns
- Mixing platform-specific conditionals throughout UI components.
- Ambiguous ownership between config, runtime logic, and native code.
- Recommending native escalation without managed-workflow validation.
- Large refactors without validation checkpoints.

## Decision Tree
- If requirement is supported in managed workflow:
  - implement with Expo APIs/config first.
- If requirement needs native capability unsupported by managed:
  - move to prebuild/config-plugin path.
- If requirement cannot be represented through Expo boundaries:
  - escalate to bare-native integration path.
- If risk is high and scope broad:
  - apply minimal targeted fix before structural refactor.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Classify request by architecture, feature, performance, or release risk.
3. Select workflow boundary (managed/prebuild/bare).
4. Define implementation design and affected files.
5. Apply minimal deterministic change strategy.
6. Define validation commands and runtime checks.
7. Document rollout and rollback considerations.
8. Produce structured output.

## Edge Cases
- Managed workflow limitation discovered late in implementation.
- Platform divergence (iOS/Android) creates inconsistent behavior.
- Config values differ between local and CI build contexts.
- Refactor introduces cross-feature dependency breakage.

## Observability
- Track build/runtime failures by platform and workflow mode.
- Track recurring boundary-escalation reasons.
- Track validation command failures after architecture changes.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Workflow boundary decision is explicit and justified.
- Architecture changes preserve typed module boundaries.
- Platform-specific behavior is isolated and testable.
- Validation steps cover both iOS and Android paths.
- Rollback path is documented for high-risk changes.

## Risks / Rollback
- Risk: wrong workflow boundary increases long-term cost.
  - Rollback: revert to prior boundary and apply scoped interim fix.
- Risk: broad architecture changes regress multiple features.
  - Rollback: partial revert to last known stable module boundaries.
