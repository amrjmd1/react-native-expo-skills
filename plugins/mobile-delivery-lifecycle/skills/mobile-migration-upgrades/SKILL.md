---
name: mobile-migration-upgrades
description: Execution-grade skill for planning and executing React Native and Expo dependency, SDK, and platform migrations with compatibility validation, rollback readiness, and controlled rollout strategy.
metadata:
  domain: mobile-delivery-lifecycle
---

# Skill: mobile-migration-upgrades

## Purpose

Design and execute React Native and Expo migrations with deterministic sequencing, compatibility validation, controlled blast radius, and rollback-ready release behavior.

## When to Use

- Upgrading Expo SDK, React Native version, or native toolchain.
- Migrating dependency groups with native/runtime coupling.
- Hardening legacy app upgrade paths before release.
- Planning canary rollout and rollback for high-risk migrations.
- Auditing migration impact on config plugins, state persistence, and OTA compatibility.

## When Not to Use

- UI-only changes with no dependency/platform migration.
- Backend-only upgrades outside mobile client runtime.
- One-off package patch with no compatibility or rollout impact.

## Required Inputs

- `target_platform`: `ios` | `android` | `both`
- `app_stack`: `expo` | `react-native-cli` | `hybrid`
- `current_version` and `target_version`
- `migration_type`: `dependency` | `sdk` | `react-native` | `native-toolchain` | `build-system` | `monorepo/package` | `mixed`
- `release_criticality`: exploratory | standard | release-blocking
- `app_size` and module complexity
- native module count and custom native code presence
- config plugin usage and plugin ordering dependencies
- OTA/update strategy and runtime coupling relevance
- persistence/storage/schema migration requirements
- auth/session migration impact
- analytics/telemetry dependency impact
- test coverage confidence
- CI/CD maturity and pipeline constraints
- rollout constraints (cohorts, geography, phased rollout)
- rollback constraints (artifact availability, data compatibility)
- deadline/urgency level
- migration context: greenfield upgrade path vs legacy stabilization

## Framework-Specific Directives

- Expo SDK migrations:
  - Treat SDK upgrades as system changes affecting config, permissions, build profiles, OTA routing, and native compatibility.
  - Validate `app.config.*`, config plugins, and generated native side effects before rollout.
  - Do not publish OTA updates that bypass binary/runtime compatibility boundaries.
- React Native version upgrades:
  - Audit Hermes, architecture mode, and native module compatibility before code changes.
  - Separate JS compatibility fixes from native/toolchain fixes in different migration phases.
  - Re-profile startup and interaction performance after upgrade.
- React Native CLI native toolchain migrations:
  - Validate Xcode/Gradle/NDK/JDK compatibility matrix before pipeline changes.
  - Require CI parity with local assumptions; reject machine-specific fixes.
- Hybrid apps:
  - Separate JS delivery path and native artifact path with explicit gate ownership.
  - Keep OTA/native release sequencing explicit when both are active.
- All contexts:
  - Separate JS-only upgrades from native rebuild-required upgrades.
  - Sequence config/plugin/toolchain changes explicitly.
  - Do not combine unrelated high-risk migrations without an isolation plan.
  - Require rollback path for every migration release candidate.

## Technical Implementation Patterns

- Migration classification model:
  - `low-risk`: isolated JS dependency patch, no runtime contract change.
  - `medium-risk`: dependency group with limited native coupling.
  - `high-risk`: framework/toolchain or architecture-adjacent change.
  - `system-wide`: SDK/RN + native/toolchain + config/state coupling.
- Compatibility audit:
  - Inventory direct and transitive dependencies.
  - Map native modules, toolchain assumptions, config/plugin coupling, and architecture flags.
  - Record explicit blockers and unblock strategy per blocker.
- Staged migration plan:
  - Split high-risk migrations into ordered phases with gated checkpoints.
  - Keep each phase reversible and independently verifiable.
- Dependency lockstep rules:
  - Define packages that must upgrade together.
  - Fail pipeline when partial upgrade introduces version drift.
- Native compatibility matrix:
  - Track platform x architecture x Hermes x toolchain x dependency compatibility.
- Config and plugin migration map:
  - Track affected config keys, plugin options/order, permissions, and generated native diffs.
- Schema/state migration rules:
  - Define migration, invalidation, or rehydration policy for persisted state, local DB, and caches.
  - Block rollout when schema compatibility rules are missing.
- Release gating for upgrades:
  - Add migration-specific gates beyond ordinary release gates (runtime checks, compatibility checks, canary health).
- Rollback readiness contract:
  - Keep a previous known-good baseline artifact.
  - Validate rollback compatibility with current persisted data.
- Migration immutability:
  - Once a migration phase is verified, its artifacts, config state, and dependency graph must not be modified retroactively.
  - Enforce forward-only migration progression.
- Parallel migration coordination:
  - Prevent concurrent migrations from modifying shared dependencies, config, or native setup simultaneously.
  - Serialize or isolate migrations affecting overlapping domains.
- Migration verification matrix:
  - Verify install, build, startup, runtime behavior, performance, analytics, crash reporting, and release safety per platform.
- Canary/staged rollout model:
  - Promote by cohorts with halt criteria and rollback triggers.

## Anti-Patterns

- Upgrading multiple unrelated high-risk systems without phase isolation.
- Skipping native module compatibility audit before SDK/RN upgrade.
- Treating TypeScript/build success as runtime safety proof.
- Leaving config plugin, permission, or generated native changes implicit.
- Upgrading without lockfile discipline and deterministic dependency resolution.
- Preserving stale persisted state after incompatible schema changes.
- Relying on local manual fixes not represented in repo/CI.
- Shipping migration releases without rollback-ready baseline.
- Treating migration releases as ordinary patch releases.
- Ignoring analytics/observability drift after migration.

## Decision Tree

- If migration is JS-only and no native/toolchain impact:
  - Use isolated dependency path with standard gates.
- If migration touches SDK/RN/toolchain/native modules:
  - Classify as high-risk or system-wide and require staged plan.
- If `app_stack == expo` and SDK migration:
  - Run config/plugin + OTA/runtime compatibility validation before rollout.
- If `app_stack == react-native-cli` and toolchain/native changes:
  - Require native compatibility matrix and CI toolchain validation.
- If lockstep dependency groups exist:
  - Upgrade as a group; reject partial version states.
- If persisted schema is incompatible:
  - Implement migration/invalidation before shipping.
- If rollback artifact is missing or incompatible:
  - Block release and create rollback baseline first.
- If risk is high/system-wide:
  - Use canary rollout with strict halt thresholds.
- If blocker exists in compatibility audit:
  - Split migration phases or hold release.
- If release signal is warning-only:
  - Continue with monitoring.
- If release-blocking failure occurs:
  - Halt promotion; rollback or re-run fixed phase.

## Execution Workflow

1. Collect required inputs.
2. Classify migration type and risk level.
3. Run compatibility audit across dependencies, native modules, and toolchain.
4. Define staged migration plan and lockstep upgrade groups.
5. Define config/plugin/schema/state migration rules.
6. Define build/runtime/performance verification matrix.
7. Define CI and release gates specific to migration.
8. Define canary/rollout and rollback plan.
9. Define observability signals for migration health.
10. Validate migration determinism, reproducibility, and blast-radius controls.
11. Produce structured output.

## Edge Cases

- Dependency upgrade compiles but fails at runtime behavior.
- Expo SDK upgrade changes plugin/config requirements unexpectedly.
- RN upgrade breaks one native module only on iOS or Android.
- Migration passes local dev but fails CI pipeline.
- Hermes/architecture behavior shifts after upgrade.
- Persisted state incompatible after release.
- OTA path incompatible with new native runtime.
- Performance regresses after framework upgrade while tests pass.
- Analytics/crash reporting/notifications silently fail post-migration.
- Rollback artifact exists but is incompatible with new persisted state.
- Transitive dependency drift introduces hidden breakage.
- Parallel pipelines produce mixed before/after migration artifacts.
- Config/plugin generated diff is larger than intended.

## Observability

- Track migration rollout success/failure rate by platform and cohort.
- Track build and runtime failures by stage and platform.
- Detect crash-rate regression after migration.
- Detect startup/runtime performance regression after migration.
- Detect config/plugin diff anomalies.
- Track native module incompatibility signals.
- Track persisted state/schema migration failures.
- Track rollback frequency and rollback reasons.
- Track canary cohort regression signals.
- Detect analytics/instrumentation event drop after migration.
- Never log secrets, tokens, or sensitive user data.

## Output Contract

- Context Summary
- Assumptions
- Migration Classification
- Compatibility Audit
- Staged Upgrade Plan
- Lockstep Dependency Groups
- Config / Plugin / Schema Migration Rules
- Verification Matrix
- Release / Canary / Rollback Plan
- Observability Signals
- Risks / Rollback
- Next Implementation Step

## Verification Checklist

- Migration type and risk level are explicit.
- Dependency and native compatibility audit is complete.
- Lockstep dependency groups are defined and enforced.
- Config/plugin changes are identified and verified.
- Persisted state/schema impact is addressed.
- CI and runtime verification cover target platforms.
- Performance and crash regression checks are included.
- Rollback target exists and is compatibility-validated.
- Rollout strategy matches migration risk.
- Observability signals are attributable during rollout.

## Risks / Rollback

- Risks:
  - Native incompatibility after framework/toolchain change.
  - Hidden transitive dependency breakage.
  - Schema/state incompatibility with upgraded runtime.
  - OTA/native runtime mismatch.
  - Performance regression after migration.
  - Observability drift masking failures.
  - Rollback incompatibility with persisted data.
- Rollback rules:
  - Keep previous known-good artifact and release metadata immutable.
  - Define clear rollback trigger thresholds (crash/perf/critical path failure).
  - Validate rollback execution path before production rollout.
  - If rollback cannot restore safe runtime/data state, halt rollout and ship corrective migration.

## Example

- Scenario:
  - Stack: Expo app.
  - Migration: SDK `N` -> `N+1` with RN-aligned dependency updates.
  - Platforms: `ios` + `android`.
- Execution:
  - Run compatibility audit for native modules, config plugins, permissions, and OTA/runtime constraints.
  - Split migration into phases: dependency lockstep -> config/plugin updates -> native rebuild + verification.
  - Promote through canary cohort first; monitor crash/performance/analytics drift.
- Rollback readiness:
  - Keep previous production artifact and update group as known-good baseline.
  - Trigger rollback if canary crosses defined crash or critical-path failure thresholds.

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
