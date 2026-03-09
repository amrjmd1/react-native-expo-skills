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
