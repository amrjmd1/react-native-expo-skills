---
name: expo-eas-release
description: Execution-grade skill for deterministic Expo EAS build, submit, rollout, and rollback-ready release workflows.
metadata:
  domain: expo-platform
---

# Skill: expo-eas-release

## Purpose
Define reliable Expo release operations across EAS Build, EAS Submit, and release rollout controls with explicit risk gates and rollback paths.

## When to Use
- Planning or executing mobile release pipelines with EAS.
- Defining `eas.json` profile strategy and environment segregation.
- Troubleshooting failed build/submit/release stages.
- Hardening rollout and rollback procedures.

## When Not to Use
- Local development-only workflows with no release intent.
- OTA-only strategy decisions without binary release coordination.

## Required Inputs
- Target platform: `ios` | `android` | `both`.
- Release type: `internal` | `preview` | `production`.
- EAS build profiles involved and profile intent.
- EAS submit usage: enabled/disabled and platform-specific submit path.
- Store targets: App Store Connect, Google Play, or both.
- `runtimeVersion` and Expo Updates channel/branch coupling policy.
- Environment/profile variable contract (required keys, defaults, fail-fast keys).
- Signing credentials ownership boundary (team-managed, CI-managed, local operator).
- Native change presence requiring binary rebuild.
- Rollout strategy: full release | phased rollout | internal testing only.
- Rollback constraints (store propagation delay, prior artifact availability, OTA fallback availability).
- CI-owned vs local operator release path.
- Artifact verification expectations (git SHA, build profile, runtimeVersion, channel/branch).

## Framework-Specific Directives
- Expo Managed:
  - Keep build profiles explicit by release type (`internal`, `preview`, `production`).
  - Treat Expo config as source of truth for release metadata and runtime policy.
  - Local builds are insufficient for production confidence unless they mirror CI profile/env contracts exactly.
- Expo + EAS:
  - Enforce profile-specific configuration (`eas.json`, env sets, secrets, channels/branches).
  - Keep CI and local paths consistent by pinning node/package manager/toolchain and profile inputs.
  - Prefer CI-owned release execution for preview/production; local operator flows should be exception-based and audited.
- Bare React Native (with EAS Build):
  - Validate native project parity with EAS profile expectations before cutting release.
  - Classify native changes explicitly; require rebuild when native code/config/plugins changed.
  - Do not assume managed profile defaults apply to bare-native artifacts.

## Technical Implementation Patterns
- Build profile contract:
  - Define immutable contract per profile: target, env map, runtime policy, credentials source, submit target.
- Release promotion path:
  - Promote `development -> preview -> production` with explicit promotion gates and freeze conditions.
- Artifact provenance checks:
  - Stamp and verify `git SHA`, profile, platform, runtimeVersion, channel/branch, build ID.
- Signing and credential boundary:
  - Keep signing credentials ownership explicit; prevent cross-profile credential reuse.
- Native rebuild trigger classification:
  - Mark changes as `binary-required` vs `OTA-safe`; block OTA-only release when binary-required triggers exist.
- Submit gating checklist:
  - Validate store metadata, permissions, credentials, version/build numbers, and compliance prerequisites before submit.
- Rollout guardrails:
  - Use phased rollout thresholds and halt criteria per platform.
- Rollback readiness verification:
  - Confirm recoverable previous artifact/update group and documented rollback operator path before promotion.
- Profile-to-env mapping:
  - Keep one-to-one mapping between release profile and env contract; reject ambiguous fallbacks.

## Anti-Patterns
- Reusing one profile for incompatible release purposes.
- Mixing preview and production credentials/config or channels.
- Shipping without explicit rollback path and known-good fallback artifact.
- Promoting artifacts without provenance and verification checks.
- Changing runtime-sensitive values without rebuild awareness.
- Coupling release correctness to local machine state.
- Skipping store metadata/permission/credential validation before submit.
- Rebuilding all stages when only one stage failed.

## Decision Tree
- If release type is `internal`:
  - use internal profile, skip store submit, validate artifact provenance and smoke checks.
- If release type is `preview`:
  - build with preview profile, optional limited submit/test tracks, enforce non-production credentials.
- If release type is `production`:
  - require CI-owned flow, full submit gating, phased rollout plan, rollback readiness proof.
- If change is `binary-required`:
  - build new binary per target platform before promotion.
- If change is `OTA-safe`:
  - allow OTA flow only when runtime/channel coupling is valid.
- If platform scope is iOS-only or Android-only:
  - run platform-specific submit/rollout checks and do not assume shared outcomes.
- If rollout strategy is phased:
  - promote incrementally with stop thresholds.
- If rollout strategy is full:
  - allow only when risk is low and rollback path is pre-validated.
- If CI and local results diverge:
  - trust CI contract; block release until mismatch is resolved.
- If same-artifact promotion is required across environments:
  - verify profile/env neutrality and provenance before reuse; otherwise rebuild per environment.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Classify release type and target platforms.
3. Map build profile to environment contract.
4. Classify whether native rebuild is required.
5. Verify signing/credential ownership and readiness.
6. Define artifact generation and provenance checks.
7. Define submit readiness and gating.
8. Define rollout and monitoring plan.
9. Define rollback path and constraints.
10. Produce structured output.

## Edge Cases
- Build succeeds locally but fails in CI for same profile.
- Production profile accidentally resolves preview env values.
- iOS builds pass while Android signing fails.
- Credentials mismatch only on one platform.
- Release artifact built with stale config/plugin output.
- `runtimeVersion`/channel mismatch breaks update path.
- Submit succeeds but rollout must be halted due to health metrics.
- Android/iOS divergence across supposedly shared release profile.
- Emergency rollback needed but previous artifact is not recoverable.
- Release cut while store metadata or permissions are stale.

## Observability
- Log build profile, platform, git SHA, runtimeVersion, channel/branch, and release candidate ID.
- Track build and submit status with store response context (without secrets/credentials).
- Detect profile/env mismatches before release promotion.
- Surface rollback readiness state before and after promotion.
- Monitor rollout metrics and halt conditions per platform.

## Output Contract
- Context Summary
- Assumptions
- Release Type / Platform Matrix
- Build Profile Contract
- Native Rebuild Classification
- Artifact Verification Plan
- Submit Readiness Checklist
- Rollout Strategy
- Rollback Plan
- Verification Plan
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Profiles map correctly to release type and platform intent.
- Profile-to-env contract is deterministic across CI and local flows.
- Credentials/signing ownership boundaries are enforced.
- Native rebuild classification is explicit and correct.
- Artifact provenance fields are present and validated.
- Submit gating checklist is complete before store submission.
- Rollout halt thresholds and rollback triggers are explicit.
- Rollback path is executable with recoverable prior artifact/update.
- Post-release verification gates are defined per platform.

## Risks / Rollback
- Risk: profile drift causes wrong build artifacts.
  - Rollback: restore known-good profile config and rebuild target platform only.
- Risk: rollout causes production regression.
  - Rollback: halt rollout, revert release path, and publish corrective update strategy.
- Risk: runtime/channel mismatch causes broken update compatibility.
  - Rollback: stop promotion, restore prior channel mapping, and rebuild with corrected runtime policy.
