---
name: expo-device-permissions
description: Execution-grade skill for implementing Expo device permission flows with deterministic runtime checks, denied-state handling, and platform-safe declarations.
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
- Feature-to-permission matrix.
- Target platforms and OS minimum versions.
- Runtime permission APIs used.
- Required config declarations and usage descriptions.
- Denied/permanently-denied UX policy.
- Background permission requirements.
- Analytics/logging constraints for permission outcomes.

## Framework-Specific Directives
- Expo Managed:
  - Use Expo API permission modules and config fields only.
  - Request contextually at point-of-need.
- Expo + EAS:
  - Validate permission copy/config across profiles and release channels.
  - Ensure build-time config is stable in CI.
- Bare React Native:
  - Keep permission wrappers platform-aware and isolated from UI components.
  - Align native manifest/plist changes with runtime request logic.

## Technical Implementation Patterns
- Centralize permission status enums and wrapper functions.
- Separate `check` and `request` operations.
- Persist last-known status for UX branching, not for security authorization.
- Gate background-dependent features behind explicit secondary checks.
- Route permanently-denied state to settings deep-link flow.

## Anti-Patterns
- Requesting all permissions at app startup.
- Repeated prompt loops after OS denial.
- Hardcoded string comparisons for permission status.
- Mixing permission logic directly inside UI view rendering.

## Decision Tree
- If permission status is granted:
  - Execute feature path.
- If status is denied and requestable:
  - Show rationale and request once at point-of-need.
- If status is permanently denied:
  - Present settings recovery path.
- If background permission is required but foreground missing:
  - Block background enablement and request foreground first.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Build permission matrix by feature and platform.
3. Define typed status model and wrapper API.
4. Implement request/check flow with denied-state UX.
5. Add config declarations and usage descriptions.
6. Validate platform differences and background behavior.
7. Verify logging/analytics for outcomes.
8. Produce structured output.

## Edge Cases
- OS returns limited/provisional permissions.
- Permission revoked in settings while app is backgrounded.
- Background permission requested before foreground grant.
- Notifications allowed on one platform but blocked on another.

## Observability
- Track permission request attempt count by feature.
- Track status transitions (`unknown -> denied -> granted`).
- Track settings-recovery flow completion rate.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Permission matrix matches feature requirements.
- iOS/Android declarations and runtime requests are aligned.
- Denied and permanently-denied flows are implemented.
- Background-dependent permissions enforce prerequisite checks.
- Permission wrappers are typed and deterministic.

## Risks / Rollback
- Risk: over-requesting harms conversion.
  - Rollback: reduce requests to point-of-need only.
- Risk: incorrect denied-state branching breaks feature access.
  - Rollback: revert to last stable wrapper and UX path.
