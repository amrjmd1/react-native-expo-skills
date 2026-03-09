---
name: mobile-api-query-architecture
description: Data and API architecture guidance for mobile apps. Use for typed API layer design, query strategy, cache ownership, retries, backoff policies, and request orchestration with React Query or equivalent tools.
---

# Mobile API Query Architecture

## Mission
Build a reliable data layer with clear ownership and predictable behavior.

## Workflow
1. Separate transport, domain mapping, and UI consumption layers.
2. Define query key strategy and cache invalidation rules.
3. Add retry/backoff and timeout policy by endpoint criticality.
4. Provide typed error normalization and UI contracts.

## Output Contract
- API Boundary Model
- Query Strategy
- Retry Policy
- Typed Contracts
- Validation Plan

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
