---
name: rn-list-performance-control
description: Advanced list rendering guidance for FlatList and virtualized UIs. Use for large datasets, pagination/infinite scroll, item memoization, key stability, layout measurement, and reducing list-related render bottlenecks.
---

# RN List Performance Control

## Mission
Keep list-heavy screens responsive under realistic data and device constraints.

## List Optimization Workflow
1. Diagnose if bottleneck is data fetch, transformation, render, or layout.
2. Apply virtualization and stable-key discipline.
3. Reduce item component render cost.
4. Validate scrolling and interaction metrics.

## Implementation Rules
- Use stable keys and avoid index keys for mutable lists.
- Memoize item rows when props are stable.
- Use `getItemLayout` when item heights are fixed or predictable.
- Keep `renderItem` closures stable.
- Avoid expensive formatting work inside row render.

## Pagination Discipline
- Decouple fetch trigger from render path.
- Guard duplicate fetches during fast scroll.
- Handle loading/error/end-of-list states explicitly.

## Output Contract
Use sections in this order:
- Bottleneck Class
- FlatList Strategy
- Row Optimization
- Pagination/State Flow
- Performance Validation

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
