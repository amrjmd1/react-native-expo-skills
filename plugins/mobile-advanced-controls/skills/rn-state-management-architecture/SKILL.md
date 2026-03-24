---
name: rn-state-management-architecture
description: Execution-grade skill for designing React Native state ownership, persistence, and server/client state boundaries with maintainable performance-safe architecture.
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
- App size: `small` | `medium` | `large`.
- Target platform: `ios` | `android` | `both`.
- Existing architecture style and current state boundaries.
- State categories involved: UI, navigation, forms, server data, session, feature/domain state.
- Current or proposed library/tooling surface (e.g., Zustand, Redux Toolkit, Jotai, React Context, TanStack Query for server state).
- Offline behavior requirements.
- Optimistic update requirements.
- Persistence/rehydration requirements.
- Multi-screen shared-state needs.
- Performance-sensitive areas and rerender hotspots.
- Testing requirements for state transitions/selectors.
- Task mode: `greenfield` | `migration`.

## Framework-Specific Directives
- Server state:
  - keep in query/cache tools (e.g., TanStack Query) with explicit invalidation policy.
- Client/domain state:
  - keep ownership feature-scoped and typed before expanding globally.
- UI state:
  - keep local/ephemeral unless multi-screen ownership is required.
- Navigation-coupled state:
  - keep route/navigation state separate from unrelated domain state.
- Form state:
  - isolate draft form state from committed domain/session state.
- Persistence:
  - isolate persistent state from transient interaction state and define explicit rehydration boundaries.

## Technical Implementation Patterns
- State ownership map:
  - map each state slice to owner (`component`, `feature store`, `global/session`, `server cache`).
- Feature-scoped store modules:
  - keep domain stores per feature with explicit public API.
- Server-state/client-state separation:
  - prevent duplicate ownership between query cache and client stores.
- Selector-based consumption:
  - use narrow selectors/subscriptions to constrain rerender surfaces.
- Persistence boundary map:
  - define what persists, storage medium, schema version, and rehydration order.
- Optimistic state staging:
  - stage optimistic values separately from committed domain state.
- Derived state minimization:
  - compute-on-read; store only source-of-truth fields.
- Navigation-state boundary:
  - route params/state remain navigation-owned, not domain-store-owned.
- Form-state isolation:
  - keep draft/edit buffers isolated from committed entities.
- Migration-safe state decomposition:
  - split monolithic stores incrementally with compatibility adapters.

## Anti-Patterns
- Putting server state into global client stores without clear reason.
- Globalizing short-lived UI interaction state.
- Duplicating same entity across multiple stores without ownership contract.
- Storing derived state that should be computed.
- Coupling navigation state with unrelated feature/domain state.
- Mixing form draft state with committed domain state.
- Unbounded context provider subscription surfaces.
- Monolithic global stores without feature segmentation.

## Decision Tree
- If state originates from server:
  - use query/cache layer first.
- If state is local component interaction only:
  - keep local component state.
- If state is shared across one feature/screen cluster:
  - use feature-scoped store module.
- If state is app-wide/session-critical:
  - use explicit global/session boundary.
- If state must persist across launches:
  - define persistence + versioned rehydration path.
- If state is optimistic interaction state:
  - stage separately and reconcile into committed state.
- If task is migration from existing global store:
  - decompose incrementally with compatibility layer.
- If screen is performance-sensitive:
  - prioritize narrow selectors/subscriptions and avoid broad context updates.

## Execution Workflow
1. Collect required inputs.
2. Classify state by taxonomy.
3. Define ownership boundaries per state category.
4. Separate server state from client state.
5. Define persistence and rehydration rules.
6. Define selectors/subscriptions/render boundaries.
7. Define optimistic and derived state rules.
8. Validate migration, performance, and testing implications.
9. Produce structured output.

## Edge Cases
- Same entity represented in query cache and client store.
- Persisted state schema changes after app update.
- Navigation remount resets expected state unexpectedly.
- Optimistic state remains unresolved longer than intended.
- Form drafts overwrite committed domain state.
- Multiple screens mutate shared feature state concurrently.
- Provider placement causes unnecessary rerender cascades.
- Migration from global Redux/store to feature stores breaks assumptions.
- Session reset fails to clear domain-dependent state.

## Observability
- Track state update frequency by owner domain (local/feature/global/query).
- Identify render hotspots caused by broad subscriptions.
- Track rehydration timing and rehydration failures.
- Detect repeated state synchronization bugs between stores/cache.
- Surface selector churn and oversized subscription surfaces.
- Detect stale optimistic state that never reconciles.
- Do not log sensitive user data.

## Output Contract
- Context Summary
- Assumptions
- State Classification
- Ownership Boundaries
- Persistence / Rehydration Rules
- Server-State vs Client-State Separation
- Performance / Subscription Strategy
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- State classification and ownership boundaries are explicit.
- Server-state and client-state separation is enforced.
- Persistence/rehydration boundaries and schema versioning are defined.
- Typed selectors/subscriptions are narrow and stable.
- Optimistic and derived state rules avoid duplication/staleness.
- Performance hotspots and rerender regressions are checked.
- Migration assumptions are validated when task mode is migration.

## Risks / Rollback
- Risk: migration introduces state inconsistency.
  - Rollback: revert affected feature boundaries to prior stable contract.
- Risk: store/query boundaries become ambiguous.
  - Rollback: reapply ownership map and isolate overlapping state paths.

## Provider-Specific Directives

- If a third-party provider is involved, bind implementation details to the provider SDK contract instead of generic assumptions.
- If provider behavior conflicts with local architecture, prefer provider-authoritative state ownership and document exceptions explicitly.
- If provider is `custom`, require explicit API contract, error model, retry policy, and lifecycle semantics before implementation.

## Example Request

"Use this skill to produce a production-safe implementation plan for this app scenario, including assumptions, architecture choices, validation steps, and rollback notes."

## Example Response Shape

- Context Summary
- Assumptions
- Implementation Plan
- Validation Checklist
- Risks / Rollback
- Next Implementation Step
