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
