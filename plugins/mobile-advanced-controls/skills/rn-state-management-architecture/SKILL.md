---
name: rn-state-management-architecture
description: Execution-grade skill for deterministic React Native state architecture with explicit server/client/UI boundaries and scalable typed ownership.
metadata:
  domain: mobile-advanced-controls
---

# Skill: rn-state-management-architecture

## Purpose
Design a predictable state architecture with explicit ownership boundaries and low render churn across server, domain, session, and UI state.

## When to Use
- Choosing state tools and boundaries for growing RN apps.
- Refactoring global state sprawl and duplicated server state.
- Defining typed selectors/actions/hooks and feature ownership.

## When Not to Use
- Small local-state-only screens.
- Backend persistence design unrelated to app state boundaries.

## Required Inputs
- State classes in scope (server, domain, session, UI).
- Current toolchain (Redux Toolkit, Zustand, React Query, Jotai, etc.).
- Feature ownership and sharing requirements.
- Performance constraints and rerender hotspots.
- Migration constraints from current architecture.

## Framework-Specific Directives
- Server state:
  - keep in query/cache tools with explicit invalidation policy.
- Client/domain state:
  - keep store boundaries feature-scoped and typed.
- UI state:
  - keep local unless shared ownership is required.

## Technical Implementation Patterns
- Create state taxonomy and ownership map.
- Select tool per state class, not one-tool-for-all.
- Expose narrow selectors and typed action APIs.
- Keep derived values computed-on-read where feasible.

## Anti-Patterns
- Duplicating server data into global client stores.
- Monolithic global stores without feature segmentation.
- Storing unstable derived data as source of truth.

## Decision Tree
- If state originates from server:
  - use query/cache layer first.
- If state is cross-feature domain logic:
  - use typed feature store boundaries.
- If state is local UI concern:
  - keep local unless clear sharing requirement exists.

## Execution Workflow
1. Collect required inputs.
2. Classify state by taxonomy.
3. Map ownership and tool boundaries.
4. Define typed selectors/actions/hooks.
5. Implement incremental migration path.
6. Validate rerender/performance impact.
7. Produce structured output.

## Edge Cases
- Same data represented in query cache and local store.
- Selector churn causing unnecessary rerenders.
- Feature migration leaves orphaned global state paths.
- Session reset fails to clear domain-dependent state.

## Observability
- Track state update frequency by store/query domain.
- Track selector subscription churn hotspots.
- Track stale/duplicated state incidents.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- State taxonomy and ownership boundaries are explicit.
- Tool choice aligns with state class.
- Typed API surface is stable and minimal.
- Rerender and stale-state regressions are checked.

## Risks / Rollback
- Risk: migration introduces state inconsistency.
  - Rollback: revert affected feature boundaries to prior stable contract.
- Risk: store/query boundaries become ambiguous.
  - Rollback: reapply ownership map and isolate overlapping state paths.
