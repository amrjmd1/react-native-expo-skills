---
name: rn-native-module-integration
description: Integration guidance for native SDKs/modules in React Native CLI projects. Use for autolinking/manual linking issues, Podfile/Gradle updates, permission wiring, initialization order, and bridge/native module compatibility troubleshooting.
---

# RN Native Module Integration

## Mission
Integrate native modules with reproducible setup and low long-term maintenance risk.

## Integration Workflow
1. Validate library compatibility with RN version and architecture.
2. Apply iOS and Android setup in deterministic order.
3. Provide typed JS wrapper around native entry points.
4. Provide rebuild and smoke test steps.

## Setup Discipline
- Keep Podfile and Gradle edits minimal and commented when non-obvious.
- Avoid global Gradle/Pod hacks when library-scoped change is possible.
- Handle permission and plist/manifest declarations together.

## Bridge Safety
- Guard calls when module is unavailable.
- Normalize native error shapes into typed JS errors.
- Provide fallback behavior for unsupported platform/device.

## Output Contract
Use sections in this order:
- Compatibility Check
- iOS Changes
- Android Changes
- JS Typed Wrapper
- Rebuild and Verification

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
