---
name: rn-animations-reanimated
description: Animation guidance for React Native with Reanimated and Moti. Use for gesture-driven interactions, transitions, layout animations, high-frequency motion paths, and performance-safe animation debugging.
---

# RN Animations with Reanimated

## Mission
Deliver smooth interactions with predictable performance on real devices.

## Animation Workflow
1. Define interaction intent and motion constraints.
2. Select primitive (`withTiming`, `withSpring`, gesture handlers, layout animation).
3. Keep critical animation work on UI thread.
4. Validate frame stability and interaction responsiveness.

## Performance Rules
- Minimize JS-thread dependency for active animations.
- Avoid coupling animation state to large React renders.
- Batch and memoize expensive style derivations.
- Verify animation behavior under list scroll and gesture contention.

## Quality Checks
- Check interruption and cancellation behavior.
- Check reduced-motion accessibility paths when applicable.
- Check low-end device responsiveness.

## Output Contract
Use sections in this order:
- Interaction Goal
- Animation Strategy
- Implementation
- Performance Risks
- Verification Steps
