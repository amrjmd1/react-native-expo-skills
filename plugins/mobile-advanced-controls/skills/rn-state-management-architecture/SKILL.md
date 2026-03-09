---
name: rn-state-management-architecture
description: State architecture guidance for React Native apps. Use for selecting and implementing Zustand, Redux Toolkit, React Query, or Jotai; defining server/client/UI state boundaries; cache strategy; and scalable feature-level state design.
---

# RN State Management Architecture

## Mission
Design a predictable state layer with clear ownership and low render churn.

## Architecture Workflow
1. Classify state as server, domain, session, or ephemeral UI state.
2. Choose tool per state class instead of one-tool-for-everything.
3. Define typed selectors/actions/hooks with stable boundaries.
4. Enforce feature-local ownership and avoid global state sprawl.

## Design Rules
- Keep server state in query/cache tools.
- Keep UI-only state close to components unless shared.
- Expose narrow selectors to reduce subscription blast radius.
- Prefer immutable update patterns for traceability.

## Anti-Patterns to Avoid
- Duplicating server state into local stores.
- Storing derived values instead of deriving on read.
- Monolithic global stores without feature segmentation.

## Output Contract
Use sections in this order:
- State Taxonomy
- Recommended Stack
- Store/Query Boundaries
- Typed API Surface
- Migration Steps

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
