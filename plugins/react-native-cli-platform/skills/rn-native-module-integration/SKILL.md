---
name: rn-native-module-integration
description: Execution-grade skill for deterministic React Native CLI native module integration, bridge safety, and cross-platform build stability.
metadata:
  domain: react-native-cli-platform
---

# Skill: rn-native-module-integration

## Purpose
Integrate native modules in RN CLI projects with deterministic iOS/Android setup, typed JS boundaries, and low-maintenance bridge behavior.

## When to Use
- Adding/upgrading native SDKs or RN native modules.
- Fixing autolinking/manual linking issues.
- Resolving Podfile/Gradle and initialization-order integration bugs.

## When Not to Use
- Pure JS dependency integration with no native changes.
- Feature work unrelated to native module behavior.

## Required Inputs
- RN version, architecture mode, and target platform scope.
- Library compatibility matrix and setup requirements.
- Required native config files touched (Podfile, Gradle, manifests, plist).
- Permission and initialization requirements.
- Rebuild/test constraints in local and CI.

## Framework-Specific Directives
- RN CLI iOS:
  - Keep Podfile and Xcode changes minimal and scoped.
- RN CLI Android:
  - Keep Gradle/manifest changes explicit and library-scoped.
- Cross-platform:
  - Normalize JS wrapper contract for platform-specific native behavior.

## Technical Implementation Patterns
- Validate compatibility before edits.
- Apply iOS and Android setup in deterministic order.
- Implement typed JS wrapper with availability guards.
- Normalize native errors into typed JS error shapes.
- Define rebuild and smoke-test sequence per platform.

## Anti-Patterns
- Global Pod/Gradle hacks for library-scoped issues.
- Calling native module without availability checks.
- Divergent iOS/Android setup with no shared wrapper contract.

## Decision Tree
- If library is incompatible with RN/version/architecture:
  - stop and choose compatible version/path.
- If autolinking fails:
  - apply minimal manual linking edits.
- If runtime bridge errors persist:
  - harden wrapper guards and initialization order.

## Execution Workflow
1. Collect required inputs.
2. Validate module compatibility and ownership boundaries.
3. Apply deterministic iOS integration changes.
4. Apply deterministic Android integration changes.
5. Implement/update typed JS wrapper.
6. Rebuild and run smoke tests.
7. Produce structured output.

## Edge Cases
- Module links on one platform but not the other.
- App builds successfully but module unavailable at runtime.
- Permission declaration missing after module integration.
- Initialization race causes intermittent native crashes.

## Observability
- Track integration failures by platform and stage.
- Track bridge availability and module init failures.
- Track wrapper-level normalized error classes.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Compatibility is validated for RN/version/architecture.
- iOS/Android integration changes are minimal and scoped.
- JS wrapper is typed and guards unavailable module states.
- Rebuild and runtime smoke checks pass.

## Risks / Rollback
- Risk: native module integration destabilizes build.
  - Rollback: revert scoped native changes and restore last stable dependency set.
- Risk: runtime crashes after integration.
  - Rollback: disable module path and fall back to guarded no-op behavior.
