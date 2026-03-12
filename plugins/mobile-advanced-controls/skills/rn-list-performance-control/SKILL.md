---
name: rn-list-performance-control
description: Execution-grade skill for diagnosing and controlling React Native list performance through virtualization, render-boundary discipline, and regression-safe measurement.
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
- Target platform: `ios` | `android` | `both`.
- List size and expected item count range.
- Item complexity and render cost profile.
- Fixed-height vs variable-height row behavior.
- Pagination/infinite-scroll usage and fetch model.
- Interaction model (feed, chat/inverted list, marketplace, timeline, settings list, etc.).
- Current list library: `FlatList` | `SectionList` | `FlashList` | custom.
- Performance symptom (jank, blank cells, memory pressure, slow first render, scroll jump).
- Refresh/update frequency and mutation pattern.
- Device class targets, including low-end devices.
- Measurement context: release vs debug build.
- Regression context if performance recently degraded.

## Framework-Specific Directives
- React Native list architecture:
  - Always evaluate performance in release mode before conclusions.
  - Preserve stable keys and stable item identity contracts.
  - Prefer virtualization-aware architecture before memoization tweaks.
- FlatList/SectionList/FlashList-compatible rules:
  - Separate list container concerns from item row rendering concerns.
  - Keep list rendering insulated from unrelated parent state churn.
  - Keep virtualization enabled unless explicit measured reason exists.
- Pagination/update flows:
  - Guard fetch concurrency and end-of-list transitions deterministically.

## Technical Implementation Patterns
- List workload classification:
  - classify as static, paginated, high-frequency feed, or inverted/chat workload.
- Container vs item render boundary split:
  - isolate container state updates from item row state/render logic.
- Stable key/item identity contract:
  - enforce deterministic keys and identity-preserving data transforms.
- Item measurement strategy:
  - use fixed-height measurement paths where possible; define fallback for variable heights.
- Virtualization window tuning:
  - tune windowing/batch props based on workload and low-end target constraints.
- Pagination trigger control:
  - gate end-reached triggers with in-flight and cursor/end-state guards.
- Render-cost isolation:
  - keep heavy work outside `renderItem`; precompute or memoize only after measurement.
- Empty/loading/error state separation:
  - isolate non-list UI states from main list render surface where possible.
- List update batching:
  - batch mutation-driven updates to reduce visible churn.
- Regression comparison workflow:
  - compare baseline vs post-change in matched release/device contexts.

## Anti-Patterns
- Using index as key for dynamic/mutable data.
- Passing unstable inline props/functions to rows without reason.
- Re-rendering entire list due to unrelated parent state changes.
- Mixing loading/error/banner UI into the same render surface as the full list when avoidable.
- Disabling virtualization without measured justification.
- Premature memoization without identifying render cause.
- Variable-height assumptions without explicit measurement strategy.
- Heavy formatting/computation inside row render.
- Unbounded fetch triggers during fast scrolling.

## Decision Tree
- If list is small and mostly static:
  - use simpler rendering path with minimal virtualization tuning.
- If list is large or frequently updated:
  - enforce virtualization and identity contracts first.
- If items are fixed-height:
  - use deterministic measurement path (`getItemLayout`/equivalent).
- If items are variable-height:
  - define measured layout strategy and blank-cell mitigation.
- If using pagination:
  - prioritize trigger guards before row-level optimization.
- If bottleneck is container-level churn:
  - isolate container state and stabilize list props.
- If bottleneck is item-level render:
  - optimize row boundaries and expensive row computation.
- If issue is memory pressure:
  - reduce retention/window size and validate long-session behavior.
- If issue is frame drops:
  - reduce per-frame render/layout cost and verify scroll smoothness.
- If issue is regression:
  - compare against previous baseline before applying new optimizations.

## Execution Workflow
1. Collect required inputs.
2. Classify list workload and performance symptom.
3. Identify container vs item render boundaries.
4. Define virtualization and measurement strategy.
5. Define item identity/key stability rules.
6. Define pagination/update behavior.
7. Run targeted performance verification.
8. Validate improvements against regressions.
9. Produce structured output.

## Edge Cases
- List performs well on simulator but fails on low-end device.
- Variable-height rows cause blank spaces or scroll jumps.
- Pagination fires repeatedly near list end.
- Frequent parent state updates re-render visible rows.
- List is smooth initially but degrades after long session.
- Inverted/chat list behaves differently from feed list.
- Item memoization hides stale UI state bugs.
- Placeholder/loading rows distort measurement assumptions.
- Large list works on iOS but stutters on Android.

## Observability
- Track dropped-frame indicators during list scroll.
- Track render counts for list container and item rows.
- Monitor memory growth during long list sessions.
- Compare first-render latency vs steady-state scroll performance.
- Detect repeated pagination trigger loops.
- Track low-end device regression cases.
- Track pagination fetch concurrency/error rates.
- Do not log sensitive user data.

## Output Contract
- Context Summary
- Assumptions
- List Workload Classification
- Virtualization / Measurement Strategy
- Render Boundary Plan
- Pagination / Update Rules
- Verification Plan
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Identity/key strategy is stable and mutation-safe.
- Container/item render boundaries are explicit and measured.
- Virtualization + measurement strategy is validated for workload type.
- Pagination/update triggers are deterministic and guarded.
- Release-mode performance is validated on target device classes.
- Cross-platform regressions are checked when scope is `both`.

## Risks / Rollback
- Risk: optimization breaks list correctness.
  - Rollback: revert row/pagination changes to last stable baseline.
- Risk: virtualization tuning regresses UX on edge devices.
  - Rollback: restore previous virtualization config and retune incrementally.
