---
name: expo-eas-release
description: Execution-grade skill for Expo EAS build, submit, and release orchestration with deterministic profile strategy, rollout control, and rollback readiness.
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
- App identifiers and platform targets.
- EAS profiles (`development`, `preview`, `production`).
- Credential/signing ownership model.
- Environment variable and secret mapping.
- Release channel/branch strategy.
- Store metadata readiness and submission constraints.
- Rollout policy and rollback triggers.

## Framework-Specific Directives
- Expo Managed:
  - Keep build profiles explicit and minimal.
  - Treat managed config as source of truth for release inputs.
- Expo + EAS:
  - Separate secrets and endpoints per profile.
  - Prefer CI-driven deterministic release commands.
- Bare React Native:
  - Validate native project parity with EAS profile expectations.
  - Isolate native build failures before rerunning full release chain.

## Technical Implementation Patterns
- Define stage gates: config validation -> build -> submit -> rollout verification.
- Use profile-specific build commands and immutable release metadata.
- Separate binary risk decisions from OTA risk decisions.
- Classify failures by stage and rerun minimal failing stage only.
- Maintain release checklist artifacts for reproducibility.

## Anti-Patterns
- Running production release commands from ad-hoc local state.
- Sharing credentials or secrets across environment scopes.
- Coupling store submission and OTA rollout without verification gates.
- Rebuilding all stages when only one stage failed.

## Decision Tree
- If config/credential validation fails:
  - stop before build and fix inputs.
- If build passes and submit fails:
  - rerun submit stage only after metadata/credential fix.
- If rollout metrics degrade:
  - halt rollout and execute rollback.
- If OTA compatibility is uncertain:
  - block OTA publish until runtime compatibility is confirmed.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Validate profile, secrets, and credential readiness.
3. Define release sequence and risk gates.
4. Execute build commands per platform/profile.
5. Execute submit commands per platform/profile.
6. Run rollout verification checks.
7. Trigger rollback if thresholds fail.
8. Produce structured output.

## Edge Cases
- iOS builds pass while Android signing fails.
- Store metadata rejection after successful binary upload.
- CI profile misconfiguration uses wrong environment secrets.
- Rollout regression appears after limited audience exposure.

## Observability
- Track build success/failure by profile and platform.
- Track submit latency and rejection reason categories.
- Track rollout health signals and rollback trigger counts.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Profiles map correctly to environment intent.
- Credentials and secrets resolve in CI.
- Build and submit commands are deterministic.
- Rollout and rollback criteria are explicit.
- Post-release verification gates are defined.

## Risks / Rollback
- Risk: profile drift causes wrong build artifacts.
  - Rollback: restore known-good profile config and rebuild target platform only.
- Risk: rollout causes production regression.
  - Rollback: halt rollout, revert release path, and publish corrective update strategy.
