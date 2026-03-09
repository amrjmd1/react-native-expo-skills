---
name: mobile-migration-upgrades
description: Migration and upgrade guidance for React Native and Expo major versions. Use for dependency compatibility analysis, staged upgrade planning, native breaking-change handling, and risk-managed rollout of platform upgrades.
---

# Mobile Migration and Upgrades

## Mission
Execute framework upgrades with minimal downtime and controlled risk.

## Workflow
1. Build compatibility matrix and blocker list.
2. Sequence upgrade in safe, reviewable increments.
3. Resolve native and tooling breakages systematically.
4. Validate behavior and performance before rollout.

## Output Contract
- Compatibility Matrix
- Upgrade Plan
- Breaking Change Fixes
- Validation Suite
- Rollout Strategy

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
