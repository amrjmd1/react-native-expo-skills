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
