---
name: rn-navigation-architecture
description: Execution-grade skill for designing React Native navigation hierarchies, route ownership, auth gating, and deep link-safe screen architecture.
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
- App size: `small` | `medium` | `large`.
- Target platform: `ios` | `android` | `both`.
- Navigation library/version and current navigator structure.
- Route types involved: `stack` | `tabs` | `drawer` | `modal` | `nested`.
- Auth flow requirements and public/private route boundaries.
- Deep-link requirements and supported entry points.
- Screen persistence/restoration expectations.
- Route parameter complexity and stability constraints.
- Feature/module ownership boundaries for routes/navigators.
- Performance-sensitive navigation surfaces.
- Context: `greenfield` | `migration`.

## Framework-Specific Directives
- React Native navigation architecture:
  - Keep feature ownership explicit at each navigator boundary.
  - Use nested navigators only when ownership and lifecycle are clear.
- React Navigation-compatible patterns:
  - Define typed route param contracts and ownership contracts per navigator.
  - Avoid central route sprawl; segment by feature modules.
- Guard flows:
  - Separate auth/public gating logic from unrelated domain state logic.
- Deep links and restoration:
  - Validate cold-start + foreground entry and restoration behavior under auth gates.

## Technical Implementation Patterns
- Route ownership map:
  - assign each route to feature owner and owning navigator.
- Navigator boundary map:
  - define app shell vs feature navigators and cross-boundary transitions.
- Auth-gated flow separation:
  - isolate auth/public trees and explicit transitions between them.
- Typed route param contracts:
  - keep param schemas stable and validated at boundaries.
- Deep-link route mapping:
  - map external paths to canonical routes and fallback behavior.
- Modal ownership rules:
  - define modal responsibilities separately from stack/tab responsibilities.
- Navigation restoration boundary:
  - define which route state is restorable and invalidation rules.
- Feature-first navigator decomposition:
  - keep feature route modules isolated with shared shell integration.
- Screen lifecycle/remount awareness:
  - document remount-sensitive routes and state reset behavior.
- Migration-safe route tree decomposition:
  - decompose monolithic trees incrementally with aliasing/compatibility guards.

## Anti-Patterns
- Stringly-typed route calls scattered across features.
- Mixing auth flow logic with unrelated navigation state.
- Duplicating route definitions across modules.
- Untyped or unstable route params.
- Deeply nested navigators without ownership rules.
- Putting domain state into navigation params without clear need.
- Excessive global navigation escape-hatch usage.
- Ambiguous overlap between modal and stack responsibilities.
- Coupling unrelated features through shared navigator internals.
- Guard logic with ambiguous redirect behavior.

## Decision Tree
- If flow is single-feature and local:
  - keep feature-owned navigator/module boundary.
- If flow spans app shell concerns:
  - place in shared shell navigator with explicit ownership.
- If route requires auth:
  - place behind auth gate; define unauthenticated redirect contract.
- If route is public:
  - keep outside auth-gated tree.
- If responsibility is transient overlay:
  - use modal boundary; avoid stack/tab ambiguity.
- If params are transient navigation hints:
  - keep in route params.
- If data is durable feature/domain state:
  - keep out of params and store in feature state.
- If migration from monolithic navigator:
  - decompose by feature ownership with compatibility aliases.
- If performance-sensitive flow:
  - minimize remount churn and cross-navigator transitions.

## Execution Workflow
1. Collect required inputs.
2. Classify route types and feature ownership.
3. Define navigator boundaries.
4. Define auth/public route separation.
5. Define route param contracts.
6. Define deep-link and restoration behavior.
7. Validate screen lifecycle/remount implications.
8. Validate performance and maintainability risks.
9. Produce structured output.

## Edge Cases
- Deep link works on cold start but fails in foreground.
- Auth state changes while user is deep in nested navigation.
- Deep link targets a screen behind auth gate.
- Route params become too large or unstable.
- Nested navigator remount resets expected screen state.
- Modal flow conflicts with back-stack expectations.
- Migration leaves duplicate route names or broken links.
- State restoration restores invalid navigation path.
- Feature module removed but route still referenced.
- Back-stack behavior diverges across nested navigators.
- iOS and Android intent handling differences.

## Observability
- Track deep-link resolution success/failure.
- Detect invalid deep-link targets and fallback usage.
- Track guard redirect loop incidents and auth-gated redirect frequency.
- Monitor screen mount/remount churn in sensitive flows.
- Detect route parameter contract violations.
- Track back-stack/navigation error events.
- Do not log sensitive user data.

## Output Contract
- Context Summary
- Assumptions
- Route Classification
- Navigator Ownership Boundaries
- Auth / Public Flow Separation
- Route Param Contracts
- Deep Link / Restoration Rules
- Performance / Lifecycle Notes
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Route and navigator ownership boundaries are explicit.
- Auth/public gating behavior is deterministic and loop-safe.
- Route param contracts are typed and stable.
- Deep-link + restoration paths are validated in startup and foreground.
- Lifecycle/remount behavior is verified for sensitive flows.
- Back-stack behavior is verified across target platforms.

## Risks / Rollback
- Risk: navigation refactor breaks route entry paths.
  - Rollback: restore previous route map and apply scoped fixes.
- Risk: deep-link behavior regresses in production.
  - Rollback: revert affected link mapping and redeploy tested config.

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
