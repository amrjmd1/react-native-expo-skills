---
name: mobile-monorepo-package-architecture
description: Monorepo and package architecture guidance for mobile teams. Use for workspace design, shared UI kits, internal package boundaries, dependency policies, and versioning strategy across apps and libraries.
---

# Mobile Monorepo Package Architecture

## Mission
Scale mobile codebases with clear package boundaries and stable dependency management.

## Workflow
1. Define workspace topology and ownership boundaries.
2. Separate app runtime code from reusable libraries.
3. Define package API contracts and versioning policy.
4. Enforce dependency graph hygiene and release workflow.

## Output Contract
- Workspace Topology
- Package Boundaries
- Versioning/Release Policy
- Tooling Rules
- Migration Plan

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
