---
name: mobile-cicd-workflows
description: CI/CD workflow guidance for React Native and Expo apps. Use for pipeline design, build caching, secret management, matrix builds, release gates, and deterministic deployment automation.
---

# Mobile CI/CD Workflows

## Mission
Create fast, reliable mobile delivery pipelines with clear release governance.

## Workflow
1. Define pipeline stages and ownership.
2. Add build/test/lint/security gates per branch strategy.
3. Configure secrets and signing material handling.
4. Define release promotion and rollback controls.

## Output Contract
- Pipeline Blueprint
- Gate Policy
- Secrets Strategy
- Release Flow
- Operational Checks

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
