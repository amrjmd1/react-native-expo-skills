---
name: expo-updates-ota
description: Execution-grade skill for safe Expo OTA update architecture including runtimeVersion control, channel routing, staged rollout, and rollback readiness.
metadata:
  domain: expo-platform
---

# Skill: expo-updates-ota

## Purpose
Define safe Expo OTA release operations with strict runtime compatibility, controlled rollout, and deterministic rollback behavior.

## When to Use
- Designing channel/branch OTA strategy.
- Publishing/promoting OTA updates across environments.
- Hardening compatibility policy between binaries and updates.
- Troubleshooting updates that fail to load/apply.

## When Not to Use
- Binary release pipeline tasks without OTA scope.
- Runtime features unrelated to update distribution.

## Required Inputs
- Target platform: `ios` | `android` | `both`.
- `runtimeVersion` policy and compatibility guard rules.
- Update channel strategy by environment.
- Branch mapping strategy and promotion path.
- Binary-required vs OTA-safe change classification.
- Environment-specific update channel separation.
- Rollout strategy: `staged` | `percentage` | `full`.
- Rollback constraints and rollback window.
- CI-owned vs manual OTA publishing flow.
- Client update policy (check/apply timing and failure behavior).
- App version/runtime coupling rules.
- Expected update blast radius (audience scope and segment).
- Previous stable update group availability.

## Framework-Specific Directives
- Expo Managed:
  - Keep update policy config-driven and version-controlled.
  - Enforce `runtimeVersion` as hard compatibility gate before every publish.
  - Do not ship OTA for changes that require binary rebuild.
- Expo + EAS:
  - Align channel/branch routing with EAS build profiles and release type.
  - Keep development/preview/production update pipelines isolated.
  - CI should own production OTA publishing and promotion.
- Bare React Native with Expo Updates:
  - Validate Expo Updates native integration and runtime mapping before publish.
  - Treat runtime and binary compatibility as hard gate across platforms.
  - Do not assume identical behavior between iOS and Android without platform validation.

## Technical Implementation Patterns
- Runtime compatibility contract:
  - Explicitly map each OTA update to allowed `runtimeVersion` set.
- Channel-to-branch routing model:
  - Define deterministic mapping per environment and release phase.
- OTA promotion path:
  - Promote `development -> preview -> production` with verification gates at each hop.
- Update group provenance:
  - Record update group ID, git SHA, runtimeVersion, branch, channel, publish actor/path.
- Staged rollout gates:
  - Use percentage or cohort-based exposure with halt thresholds.
- Rollback recovery flow:
  - Pre-validate rollback target group and operator commands before promotion.
- Update health monitoring:
  - Monitor adoption, load/apply failures, crash/error deltas, and incompatible load signals.
- Update freeze rules:
  - Freeze promotion/publish during overlapping binary release risk windows or incident response.

## Anti-Patterns
- Publishing OTA without `runtimeVersion` validation.
- Mixing preview and production channels/branches.
- Bypassing CI release controls for production OTA.
- Pushing OTA after binary-required changes.
- Expanding blast radius without staged gates.
- Publishing without rollback-ready previous update group.
- Environment-dependent routing that changes between local and CI.
- Starting triage without update group provenance context.

## Decision Tree
- If change is binary-required:
  - block OTA publish and require binary rebuild path.
- If change is OTA-safe:
  - continue with runtime/channel compatibility validation.
- If release stage is development:
  - publish to development branch/channel with limited audience.
- If release stage is preview:
  - publish to preview branch/channel and run staged verification.
- If release stage is production:
  - require CI-owned publish/promotion with rollback target confirmed.
- If emergency hotfix:
  - use constrained blast radius first, then promote only after health checks.
- If target is single-platform:
  - validate platform-specific runtime compatibility and rollout independently.
- If rollout strategy is staged:
  - increase exposure only when health gates pass.
- If rollout strategy is full:
  - allow only when risk is low and rollback readiness is confirmed.
- If manual operator patch is required:
  - require explicit approval, provenance capture, and post-publish verification.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Classify change as OTA-safe or binary-required.
3. Validate `runtimeVersion` compatibility.
4. Determine channel and branch routing.
5. Define update blast radius.
6. Validate previous update rollback availability.
7. Publish OTA candidate.
8. Monitor update health metrics.
9. Promote or rollback update.
10. Produce structured output.

## Edge Cases
- OTA published with wrong `runtimeVersion`.
- Client receives incompatible update payload.
- Update published to wrong channel.
- Binary and OTA release overlap incorrectly.
- Staged rollout exposes regression.
- Update fails to load on specific device cohorts.
- Rollback required but previous update group missing.
- Preview update accidentally promoted to production.
- Binary in field cannot accept latest OTA runtime.
- Asset/runtime crash appears only after promotion.
- Rollback command succeeds but clients cache stale update state.

## Observability
- Track update group IDs and release candidate identifiers.
- Log `runtimeVersion`, channel, branch, platform, and publish metadata per update.
- Monitor OTA adoption rate by runtime/platform/channel.
- Detect crash/error spikes and incompatible update loads after publish.
- Capture publish/promotion/rollback events with actor and CI/manual source.
- Detect routing mismatches before and after promotion.
- Never log secrets or credentials.

## Output Contract
- Context Summary
- Assumptions
- Runtime Compatibility Model
- Channel / Branch Routing
- OTA Safety Rules
- Rollout Strategy
- Rollback Plan
- Verification Plan
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- `runtimeVersion` compatibility gate is enforced before publish.
- Channel/branch routing is deterministic by environment and release stage.
- OTA-safe vs binary-required classification is explicit.
- Staged rollout gates and blast-radius controls are defined.
- Rollback target group exists and rollback path is executable.
- CI/manual ownership policy for publish/promotion is enforced.
- Health monitoring and halt thresholds are defined per platform.
- Update provenance metadata is captured for every publish.

## Risks / Rollback
- Risk: incompatible update causes startup/runtime failure.
  - Rollback: revert to last stable update group and pause promotion.
- Risk: channel misrouting affects unintended users.
  - Rollback: restore prior channel mapping and republish corrected target.
- Risk: production publish bypasses CI controls.
  - Rollback: halt promotion, reissue publish through CI path with validated provenance.
