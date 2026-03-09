---
name: rn-navigation-architecture
description: Navigation architecture guidance for Expo Router or React Navigation in complex apps. Use for nested navigation composition, route typing, deep linking, auth gating, modal stacks, and scalable navigation boundaries.
---

# RN Navigation Architecture

## Mission
Create navigation systems that stay typed, debuggable, and evolvable as app complexity grows.

## Navigation Workflow
1. Model route tree by product domain and ownership.
2. Define route param types and shared navigation contracts.
3. Implement auth/onboarding gating without route ambiguity.
4. Validate deep links and edge-route behavior.

## Architecture Rules
- Keep route naming consistent and stable.
- Separate app shell, auth, and modal concerns.
- Avoid cross-feature navigation coupling through implicit string routes.
- Provide typed helper APIs for high-traffic navigation paths.

## Reliability Checks
- Verify cold-start deep links.
- Verify foreground/background link handling.
- Verify back-stack behavior across nested navigators.

## Output Contract
Use sections in this order:
- Route Model
- Typed Contracts
- Guarding Strategy
- Deep Link Plan
- Verification Cases

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
