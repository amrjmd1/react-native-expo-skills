---
name: rn-list-performance-control
description: Execution-grade skill for deterministic FlatList and virtualized list performance control under large data and pagination pressure.
metadata:
  domain: mobile-advanced-controls
---

# Skill: rn-list-performance-control

## Purpose
Keep list-heavy screens responsive by controlling virtualization, row render cost, and pagination behavior under real device constraints.

## When to Use
- Optimizing large lists with scrolling jank.
- Fixing duplicate pagination fetches or list render churn.
- Designing stable list architecture for high-volume datasets.

## When Not to Use
- Small static lists with no performance pressure.
- Performance issues unrelated to list rendering.

## Required Inputs
- Dataset size and mutation frequency.
- Target platform/device constraints.
- List primitive and virtualization settings.
- Item rendering complexity and key strategy.
- Pagination/infinite-scroll behavior requirements.

## Framework-Specific Directives
- FlatList/virtualization:
  - Use stable keys and deterministic windowing settings.
- Row rendering:
  - Keep row props minimal and memoization boundaries explicit.
- Pagination:
  - Guard fetch concurrency and end-of-list state transitions.

## Technical Implementation Patterns
- Classify bottleneck: data transform, render, layout, or fetch.
- Stabilize `renderItem`, keys, and row props.
- Use `getItemLayout` for predictable row heights.
- Separate pagination triggers from render path.

## Anti-Patterns
- Index keys for mutable data sets.
- Heavy formatting/computation inside row render.
- Unbounded fetch triggers during fast scrolling.

## Decision Tree
- If bottleneck is render-cost:
  - optimize row component boundaries first.
- If bottleneck is layout:
  - provide predictable measurement strategy.
- If bottleneck is pagination:
  - fix fetch trigger/guard logic before UI changes.

## Execution Workflow
1. Collect required inputs.
2. Classify list bottleneck type.
3. Apply virtualization and key stability rules.
4. Reduce row render cost and closure churn.
5. Harden pagination state/fetch flow.
6. Validate scroll and interaction metrics.
7. Produce structured output.

## Edge Cases
- Smooth list in debug but janky in release on low-end devices.
- Duplicate fetches on rapid scroll.
- One platform shows heavier layout cost.
- Dynamic row heights break measurement assumptions.

## Observability
- Track dropped-frame indicators during list scroll.
- Track row rerender count in hot paths.
- Track pagination fetch concurrency/error rates.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Key strategy is stable and mutation-safe.
- Row rendering cost is reduced and measurable.
- Pagination triggers are deterministic and guarded.
- Cross-platform scroll performance is validated.

## Risks / Rollback
- Risk: optimization breaks list correctness.
  - Rollback: revert row/pagination changes to last stable baseline.
- Risk: virtualization tuning regresses UX on edge devices.
  - Rollback: restore previous virtualization config and retune incrementally.
