---
name: rn-navigation-architecture
description: Execution-grade skill for scalable React Native navigation architecture with typed routing, deep-link reliability, and deterministic flow boundaries.
metadata:
  domain: mobile-advanced-controls
---

# Skill: rn-navigation-architecture

## Purpose
Design typed, maintainable navigation systems with explicit route ownership, guard logic, and reliable deep-link behavior.

## When to Use
- Defining nested navigator or Expo Router route structure.
- Hardening auth/onboarding gating and modal flow boundaries.
- Fixing deep-link and back-stack reliability issues.

## When Not to Use
- Single-screen apps with trivial navigation.
- Non-navigation runtime issues.

## Required Inputs
- Navigation library/approach in use.
- Route hierarchy and ownership boundaries.
- Param contracts and deep-link requirements.
- Auth/onboarding gating requirements.
- Platform-specific navigation behavior constraints.

## Framework-Specific Directives
- React Navigation/Expo Router:
  - Keep route contracts typed and centralized.
- Guard flows:
  - Keep auth and onboarding gating deterministic.
- Deep links:
  - Validate cold-start and foreground handling paths.

## Technical Implementation Patterns
- Model route tree by domain ownership.
- Define typed param contracts and helper APIs.
- Separate app shell/auth/modal flows.
- Use explicit deep-link mapping and fallback routes.

## Anti-Patterns
- Stringly-typed route calls scattered across features.
- Coupling unrelated features through shared navigator internals.
- Guard logic with ambiguous redirect behavior.

## Decision Tree
- If route contract is unstable:
  - define typed route/param model first.
- If issue is auth gating:
  - isolate guard flow before route refactor.
- If issue is deep-link handling:
  - validate mapping and startup/foreground entry paths.

## Execution Workflow
1. Collect required inputs.
2. Model route ownership and hierarchy.
3. Define typed route and param contracts.
4. Implement guard and modal boundary rules.
5. Validate deep-link and back-stack behavior.
6. Verify iOS/Android parity.
7. Produce structured output.

## Edge Cases
- Deep link works on cold start but fails in foreground.
- Back-stack behavior diverges across nested navigators.
- Auth redirect loop after token state change.
- iOS and Android intent handling differences.

## Observability
- Track deep-link resolution success/failure.
- Track guard redirect loop incidents.
- Track back-stack/navigation error events.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Route ownership and contracts are explicit.
- Guarding logic is deterministic.
- Deep-link mappings are validated in startup and foreground.
- Back-stack behavior is verified across target platforms.

## Risks / Rollback
- Risk: navigation refactor breaks route entry paths.
  - Rollback: restore previous route map and apply scoped fixes.
- Risk: deep-link behavior regresses in production.
  - Rollback: revert affected link mapping and redeploy tested config.
