---
name: rn-animations-reanimated
description: Execution-grade skill for designing React Native Reanimated interactions with explicit thread ownership, worklet boundaries, and performance-safe animation control.
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
- Target platform: `ios` | `android` | `both`.
- Interaction goal: `gesture` | `transition` | `layout` | `continuous loop` | `microinteraction`.
- Animation primitives in scope (`withTiming`, `withSpring`, `withDecay`, layout transitions, gestures).
- Current performance/correctness symptom (frame drops, stutter, delayed response, desync, interruption artifact).
- Thread ownership assumptions (UI-thread vs JS-thread responsibilities).
- Reduced-motion/accessibility requirements.
- Device class targets, including low-end devices.
- Measurement context: release vs debug build.
- Driver type: user-driven | time-driven | state-driven.
- Need for layout animation vs shared-value animation path.
- Regression context if behavior/performance recently changed.

## Framework-Specific Directives
- Reanimated core:
  - Keep high-frequency animation logic on UI thread.
  - Use worklets intentionally and keep them deterministic.
  - Keep shared values and derived values scoped with explicit ownership.
- Gesture integration:
  - Isolate gesture loops from unrelated React state updates.
  - Define explicit interruption/cancellation behavior.
- Thread boundary discipline:
  - Avoid unnecessary JS-thread coupling during active animations.
- Validation mode:
  - Validate animation smoothness/correctness in release builds, not debug-only contexts.
- Accessibility:
  - Treat reduced-motion support as first-class and deterministic.
- Cross-platform:
  - Validate parity under iOS and Android interaction timing differences.
- Platform timing differences:
  - Account for differences in gesture velocity, spring physics, and event timing.
  - Tune animation parameters per platform when needed.

## Technical Implementation Patterns
- Animation classification model:
  - classify by gesture-driven, state-driven, layout-driven, or continuous-loop behavior.
- Interaction contention model:
  - define how gesture, scroll, navigation, and layout animation sources compete.
  - establish priority rules (e.g., gesture overrides transition).
  - ensure deterministic resolution of competing animation drivers.
- UI-thread vs JS-thread ownership map:
  - assign owner for each animation path and side-effect boundary.
- Animation ownership transfer:
  - define ownership transfer across gesture -> state -> layout paths.
  - ensure shared values are not controlled by multiple sources simultaneously.
  - enforce single-writer principle for animation-critical values.
- runOnJS boundary discipline:
  - use `runOnJS` only for low-frequency side effects such as analytics, navigation triggers, or state synchronization after animation completion.
  - avoid invoking `runOnJS` inside high-frequency animation loops or gesture frames.
- Worklet-safe computation boundary:
  - keep frame-critical computations in worklets; move non-critical logic out of frame loop.
- Shared-value ownership model:
  - define source-of-truth shared values and update authority per interaction.
- Derived-value minimization:
  - reduce per-frame derived computation cost under high interaction rates.
- Frame budget awareness:
  - ensure per-frame work stays within safe execution limits (~16ms for 60fps).
  - identify heavy derived calculations and move them off the critical path.
  - treat frame drops as system-level failure, not minor degradation.
- Gesture-driven animation path:
  - keep gesture -> shared value -> animated style pipeline isolated from React rerenders.
- State-driven transition path:
  - map discrete state changes to deterministic one-shot transition contracts.
- Layout animation boundary:
  - isolate layout transitions from simultaneously controlled gesture transforms.
- Interruption/cancellation rules:
  - define cancel/reset/snap behavior for partial gestures and competing animations.
- Gesture cancellation strategy:
  - define behavior when gesture is aborted mid-flight.
  - ensure UI resolves into a valid stable state (snap-back, settle, or continue).
  - avoid leaving values in intermediate invalid states.
- Completion signal contract:
  - define idempotent completion behavior for animation side effects.
  - completion side effects must not fire more than once.
  - cancellation/interruption must not trigger completion logic.
  - guard against race conditions between completion and cancellation.
- Resource safety:
  - ensure animations do not retain references after unmount.
  - avoid leaking listeners or shared values across screens.
  - validate cleanup under rapid navigation.
- Before/after profiling workflow:
  - compare baseline vs post-change frame behavior in matched release/device contexts.

## Anti-Patterns
- Driving high-frequency animation from JS thread state.
- Coupling animation values to broad component rerenders.
- Bridging animation state back to React state during active interaction without reason.
- Mixing layout, gesture, and state-driven paths without ownership rules.
- Heavy derived calculations every frame.
- Skipping interruption/cancellation path validation.
- Ignoring reduced-motion requirements.
- Validating animation smoothness only in debug mode.

## Decision Tree
- If animation is gesture-driven:
  - keep interaction loop UI-thread-owned with worklet-safe updates.
- If animation is state-driven one-shot transition:
  - use deterministic transition contract and minimal JS coupling.
- If layout change drives motion:
  - use layout animation path with explicit ownership boundaries.
- If animation is continuous:
  - optimize steady-state frame cost and lifecycle cleanup.
- If issue is frame-drop/jank:
  - profile frame-critical path and reduce per-frame work first.
- If issue is logic/correctness:
  - validate interruption/cancellation and state synchronization rules.
- If issue is baseline behavior:
  - establish release-mode baseline before optimization.
- If issue is regression:
  - compare against last stable baseline and isolate changed boundary.

## Execution Workflow
1. Collect required inputs.
2. Classify animation type and interaction model.
3. Define thread ownership and worklet boundaries.
4. Define shared/derived value responsibilities.
5. Define interruption/cancellation behavior.
6. Define reduced-motion fallback rules.
7. Run targeted profiling in release mode.
8. Validate correctness and performance across target platforms.
9. Produce structured output.

## Edge Cases
- Gesture animation smooth on iOS but stutters on Android.
- Animation appears correct in debug but fails in release.
- Shared values remain active after component unmount.
- Interruption leaves UI in invalid intermediate state.
- Derived values cause frame drops under rapid interaction.
- Layout transitions conflict with gesture-driven movement.
- Reduced-motion mode disables expected feedback incorrectly.
- Continuous animation degrades performance over long sessions.
- Scroll + animation contention causes frame drops.
- Rapid navigation during animation causes inconsistent state.
- Gesture interrupted by navigation transition.
- Animation completes after screen unmount.
- Multiple animations compete on same shared value.
- Layout + transform conflict causes visual glitch.

## Observability
- Track dropped-frame indicators during animation scenarios.
- Track animation interruption vs completion ratio.
- Compare release-build animation smoothness across device classes.
- Detect repeated frame drops under identical interaction paths.
- Detect repeated interruption/cancellation failure cases.
- Track animation completion vs cancellation ratios to detect interaction interruption bugs or unstable motion contracts.
- Detect long-lived animations beyond expected lifecycle.
- Record first-interaction animation performance when relevant.
- Surface thread-boundary mistakes correlated with jank.
- Correlate jank with specific animation paths.
- Track platform-specific animation regressions.
- Do not log sensitive user data.

## Output Contract
- Context Summary
- Assumptions
- Animation Classification
- Thread Ownership / Worklet Boundaries
- Shared / Derived Value Plan
- Interruption / Cancellation Rules
- Reduced-Motion Strategy
- Verification Matrix
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Thread ownership is explicit for critical animation paths.
- Worklet boundaries are deterministic and side-effect-safe.
- No shared value has multiple active writers.
- No animation remains active after ownership ends.
- Interruption/cancellation behavior is deterministic.
- Completion/cancellation semantics are correct.
- Reduced-motion behavior is handled when applicable.
- Frame stability is validated on target platforms and device classes.
- Frame stability holds under rapid interaction.
- Cross-platform behavior is validated.
- Regression checks compare baseline and post-change in release mode.

## Risks / Rollback
- Risk: animation changes degrade interaction responsiveness.
  - Rollback: revert to prior stable motion contract and reprofile hotspots.
- Risk: platform-specific regressions after rollout.
  - Rollback: disable affected animation path behind feature flag.

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
