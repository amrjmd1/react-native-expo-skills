---
name: expo-device-permissions
description: Execution-grade skill for safe Expo device permission architecture including declaration correctness, runtime request strategy, denied-state UX, and store compliance.
metadata:
  domain: expo-platform
---

# Skill: expo-device-permissions

## Purpose
Implement production-safe permission architecture for Expo apps with explicit runtime state handling, denied/permanently-denied UX paths, and platform-correct config declarations.

## When to Use
- Adding camera, location, notifications, media, sensor, or background permissions.
- Refactoring permission checks/request flows.
- Fixing denied-state loops and settings recovery UX.
- Auditing iOS/Android permission parity.

## When Not to Use
- Features with no OS permission requirements.
- Backend authorization or role-based access unrelated to device permissions.

## Required Inputs
- Target platform: `ios` | `android` | `both`.
- Feature requiring permission and whether permission is required vs optional.
- Permission type(s): camera, microphone, location (foreground/background), notifications, media, sensors, contacts, etc.
- Declaration location and owner: Expo config/plugin vs native config overlays.
- Runtime request strategy: trigger point, rationale copy, pre-check policy.
- Denied/permanently-denied/restricted UX flow.
- Feature fallback/degradation behavior when unavailable.
- Store compliance constraints (purpose strings, policy-sensitive permissions, review notes).
- Platform-specific permission behavior differences and OS version constraints.
- Permission retry policy (timing, limits, settings redirect policy).
- Permission state caching/tracking strategy.
- Analytics/logging constraints for permission outcomes.

## Framework-Specific Directives
- Expo Managed:
  - Declare required permissions in Expo config before any runtime request path.
  - Use Expo permission APIs and request only after clear user intent.
  - Keep usage-description copy accurate and policy-safe per permission.
- Expo + EAS:
  - Validate permission declarations/copy per profile and release channel.
  - Keep permission-related config deterministic across local and CI builds.
  - Treat CI as source of truth for release-bound permission config validation.
- Bare React Native with Expo APIs:
  - Keep Expo permission API wrappers platform-aware and isolated from UI components.
  - Align Info.plist/AndroidManifest declarations with runtime request logic.
  - Do not request permissions not declared in final native config artifacts.

## Technical Implementation Patterns
- Permission gating architecture:
  - Gate feature entry on typed permission state (`granted`, `denied`, `blocked`, `restricted`, `unavailable`).
- Lazy request strategy:
  - Trigger permission request from explicit user action at point-of-need.
- Denied-state fallback UX:
  - Provide alternate flow, explanation, and next action for denied/blocked states.
- Feature degradation pattern:
  - Disable only permission-dependent sub-features; preserve non-dependent paths.
- Permission state tracking:
  - Centralize `check` and `request` wrappers with stable status mapping per platform.
- Platform-specific handling:
  - Implement platform branches for behavior differences (e.g., notifications, background location).
- Request throttling:
  - Limit repeated prompts and debounce repeated requests from rapid UI interactions.
- Retry UX:
  - Allow user-initiated retry with contextual guidance; use settings deep-link for permanently denied.

## Anti-Patterns
- Requesting permissions on app launch without context.
- Declaring permissions not used by shipped features.
- Failing to handle denied/restricted/permanently denied states.
- Assuming permission behavior is identical across iOS and Android.
- Requesting multiple unrelated permissions in one flow.
- Ignoring store policy requirements for sensitive permissions.
- Mixing permission logic directly into UI rendering without centralized wrappers.

## Decision Tree
- If permission is already granted:
  - Execute feature path.
- If permission is not granted and requestable:
  - Show rationale and request at user intent boundary.
- If permission is denied:
  - Offer retry path if feature is required; otherwise continue with fallback.
- If permission is permanently denied/blocked:
  - Route to settings recovery flow and fallback UX.
- If permission is restricted by policy/device controls:
  - Skip request loop and present non-retryable fallback messaging.
- If permission is required for core feature:
  - Block protected action until permission granted or user exits flow.
- If permission is optional:
  - Degrade feature and continue app flow.
- If platform behavior diverges:
  - Use platform-specific branch and validate separately.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Classify permission type and feature dependency.
3. Validate permission declaration location and correctness.
4. Define runtime request trigger and rationale UX.
5. Design denied-state and permanently-denied fallback flows.
6. Implement permission state tracking and request throttling.
7. Test platform-specific behavior and edge states.
8. Verify store compliance constraints and copy.
9. Monitor permission-related failures and denial loops.
10. Produce structured output.

## Edge Cases
- Permission denied permanently by user.
- Permission restricted by device policy/parental controls/MDM.
- Permission revoked after app installation.
- User enables permission in settings and returns to app.
- Permission request fails due to platform bug/transient OS error.
- Permission available on one platform but unavailable on another.
- Permission granted but feature fails due to hardware constraints.
- OS returns limited/provisional permission states.
- Background permission requested before prerequisite foreground grant.

## Observability
- Track permission request rate and grant/deny/blocked/restricted outcome rates by feature.
- Monitor feature failures attributable to missing permission state.
- Detect repeated denial loops and repeated prompt attempts.
- Log request context (feature, platform, trigger point) and outcome status.
- Monitor permission-related crashes and recovery success after settings deep-link.
- Never log sensitive user data.

## Output Contract
- Context Summary
- Assumptions
- Permission Model
- Runtime Request Strategy
- Denied-State UX
- Feature Fallback Strategy
- Store Compliance Considerations
- Verification Plan
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Permission matrix matches feature dependency and platform scope.
- Declarations exist before runtime requests on each platform.
- iOS/Android runtime behavior differences are explicitly handled.
- Denied, blocked, restricted, and permanently-denied flows are implemented.
- Fallback strategy works for required vs optional features.
- Request throttling prevents prompt loops.
- Store policy-sensitive permissions have compliant usage copy and rationale.
- Permission wrappers and state mapping are typed and deterministic.

## Risks / Rollback
- Risk: over-requesting harms conversion.
  - Rollback: reduce requests to point-of-need only.
- Risk: incorrect denied-state branching breaks feature access.
  - Rollback: revert to last stable wrapper and UX path.
- Risk: store rejection due to inaccurate permission declarations/copy.
  - Rollback: revert to policy-compliant declaration set and resubmit with corrected metadata.

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
