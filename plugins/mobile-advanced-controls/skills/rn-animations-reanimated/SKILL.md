---
name: rn-animations-reanimated
description: Execution-grade skill for designing Reanimated animation systems with deterministic motion behavior and performance-safe interaction handling.
metadata:
  domain: mobile-advanced-controls
---

# Skill: rn-animations-reanimated

## Purpose
Implement smooth, deterministic React Native animations with Reanimated while controlling UI-thread cost and interaction reliability.

## When to Use
- Building gesture-driven or transition-heavy interactions.
- Debugging dropped frames or animation interruption bugs.
- Defining animation architecture for complex screens.

## When Not to Use
- Static UI updates with no animation behavior.
- Performance issues unrelated to animation or interaction loops.

## Required Inputs
- Target platform (`ios`, `android`, `both`).
- Interaction goal and motion constraints.
- Animation primitives in scope (`withTiming`, `withSpring`, layout animations, gestures).
- Device class/performance constraints.
- Accessibility/reduced-motion requirements.

## Framework-Specific Directives
- Reanimated core:
  - Keep high-frequency animation logic on UI thread.
  - Keep shared values and derived values scoped.
- Gesture integration:
  - Define explicit interruption/cancellation behavior.
- Cross-platform:
  - Validate parity under iOS and Android interaction timing differences.

## Technical Implementation Patterns
- Classify animation by interaction type (gesture, transition, loop, layout).
- Separate animation state from heavy React render state.
- Use stable animation contracts per component boundary.
- Validate under scroll + gesture contention.

## Anti-Patterns
- Driving high-frequency animation from JS thread state.
- Coupling animation values to broad component rerenders.
- Skipping interruption/cancellation path validation.

## Decision Tree
- If animation is high-frequency and interaction-critical:
  - keep logic on UI thread and minimize JS dependency.
- If animation is layout-driven:
  - use layout animation with explicit fallback behavior.
- If frame stability fails:
  - reduce derived style cost before changing motion design.

## Execution Workflow
1. Collect required inputs.
2. Classify animation type and constraints.
3. Select animation primitives and thread ownership.
4. Implement scoped animation state contracts.
5. Validate interruption, cancellation, and contention behavior.
6. Verify frame stability on target platforms/devices.
7. Produce structured output.

## Edge Cases
- Animation smooth on simulator but janky on physical device.
- Gesture interruption leaves stale animation state.
- Scroll + animation contention causes frame drops.
- Reduced-motion setting conflicts with default motion path.

## Observability
- Track dropped-frame indicators during animation scenarios.
- Track interruption/cancellation failure cases.
- Track platform-specific animation regressions.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Thread ownership is explicit for critical animation paths.
- Interruption/cancellation behavior is deterministic.
- Reduced-motion behavior is handled when applicable.
- Frame stability is validated on target platforms.

## Risks / Rollback
- Risk: animation changes degrade interaction responsiveness.
  - Rollback: revert to prior stable motion contract and reprofile hotspots.
- Risk: platform-specific regressions after rollout.
  - Rollback: disable affected animation path behind feature flag.
