---
name: rn-native-module-integration
description: Execution-grade skill for deterministic React Native CLI native module and SDK integration with typed wrappers, minimal native drift, and repeatable verification.
metadata:
  domain: react-native-cli-platform
---

# Skill: rn-native-module-integration

## Purpose
Integrate native modules in RN CLI projects with deterministic iOS/Android setup, typed JS boundaries, and low-maintenance bridge behavior.

## When to Use
- Adding/upgrading native SDKs or RN native modules.
- Fixing autolinking/manual linking issues.
- Resolving Podfile/Gradle and initialization-order integration bugs.

## When Not to Use
- Pure JS dependency integration with no native changes.
- Feature work unrelated to native module behavior.

## Required Inputs
- Target platform: `ios` | `android` | `both`.
- React Native version.
- New Architecture enabled or disabled.
- Hermes enabled or disabled.
- SDK/module type: RN package | native iOS SDK | native Android SDK | hybrid.
- Autolinking support status and manual-link requirements.
- CocoaPods, Gradle, Maven repository requirements.
- Required native capabilities (permissions, background modes, URL schemes, plist/manifest keys).
- Required JS API surface and expected error model.
- Local vs CI build paths and toolchain constraints.
- Existing manual native patches, if any.
- Integration criticality: exploratory vs release-blocking.

## Framework-Specific Directives
- React Native CLI default workflow:
  - Prefer autolinking unless package docs require manual native integration.
  - Keep native diffs minimal, scoped, and documented.
- RN CLI + New Architecture:
  - Validate TurboModule/Fabric compatibility before installation.
  - If dependency is legacy-only, gate integration path and document bridge fallback.
- RN CLI with native-heavy ownership:
  - Keep explicit ownership map for `ios/` and `android/` files touched.
  - Separate SDK installation tasks from JS wrapper implementation tasks.
- iOS-specific integration path:
  - Apply Podfile/Xcode changes first, run pod sync checkpoint, then runtime verification.
- Android-specific integration path:
  - Apply Gradle/Maven/manifest changes first, run sync checkpoint, then runtime verification.
- CI/local parity rule:
  - Verify integration under same dependency/toolchain assumptions as CI before declaring success.

## Technical Implementation Patterns
- Deterministic setup order:
  - compatibility check -> dependency install -> native config -> package manager sync -> wrapper -> verification.
- Dependency installation contract:
  - Pin package/native dependency versions and required repositories explicitly.
- Pod/Gradle sync checkpoints:
  - Capture pass/fail after each native package manager step before proceeding.
- Typed JS wrapper boundary:
  - Expose stable typed API; hide native error/shape variability inside adapter layer.
- Native capability declaration map:
  - Track capability key -> platform -> file -> owning module.
- Installation verification matrix:
  - Validate debug/release x iOS/Android outcomes against expected API behavior.
- Native diff review:
  - Review only touched native files and reject unrelated drift.
- Upgrade-safe wrapper isolation:
  - Keep module-specific wrapper isolated from app business logic.
- Ownership map for touched files:
  - Maintain list of changed files and rationale for each integration step.
- Integration rollback plan:
  - Define reversible checkpoints for dependency, native config, and wrapper changes.

## Anti-Patterns
- Editing unrelated native files during module integration.
- Mixing installation steps with app-level business logic changes.
- Depending on local machine state not represented in repo/CI.
- Bypassing autolinking without explicit reason.
- Wrapping native APIs with untyped/leaky JS contracts.
- Introducing config drift between iOS and Android for shared behavior.
- Validating only one platform when both are target scope.
- Global Pod/Gradle hacks for library-scoped issues.

## Decision Tree
- If library is incompatible with RN/version/architecture:
  - stop and choose compatible version/path.
- If module supports autolinking:
  - use autolinking path; apply manual steps only if documented required.
- If module requires manual integration:
  - apply platform-scoped native changes with ownership map.
- If integration type is RN package:
  - prioritize wrapper isolation and capability declarations.
- If integration type is direct native SDK:
  - prioritize native package manager/repository correctness before JS exposure.
- If target is iOS-only or Android-only:
  - verify only required platform but document non-target assumptions.
- If integration is release-blocking:
  - require full debug/release verification and CI parity checks.
- If integration is exploratory:
  - allow scoped PoC path with explicit non-production constraints.
- If New Architecture compatibility is required:
  - block merge until compatibility is validated or fallback plan is defined.
- If legacy bridge is acceptable:
  - proceed with guarded wrapper and migration note.

## Execution Workflow
1. Collect required inputs.
2. Classify integration type and target platforms.
3. Identify required native capabilities and files touched.
4. Decide autolink vs manual integration path.
5. Install dependencies and sync native package managers.
6. Define typed JS wrapper boundary.
7. Verify native configuration changes.
8. Run build and runtime verification on each target platform.
9. Compare local and CI expectations.
10. Produce structured output.

## Edge Cases
- iOS integrates successfully but Android fails due to manifest/Gradle mismatch.
- Android works locally but CI fails due to missing repository/signing context.
- Autolinking partially succeeds but manual native step is still required.
- `pod install` succeeds but runtime symbol is missing.
- Gradle sync passes but runtime initialization fails.
- New Architecture build breaks for legacy dependency.
- Package upgrade changes native setup requirements.
- Local manual patch is overwritten during reinstall.
- One platform needs capability declaration the other does not.
- Integration succeeds in debug but fails in release.

## Observability
- Record files touched during integration and owning step.
- Track platform-specific build/runtime verification status.
- Capture autolink vs manual-link decision and rationale.
- Surface native capability declarations added/changed.
- Detect local-vs-CI integration divergence.
- Detect cases where build succeeds but runtime initialization fails (e.g. native symbol missing, SDK not initialized, platform-specific capability mismatch).
- Log verification matrix results (without secrets).

## Output Contract
- Context Summary
- Assumptions
- Integration Type / Platform Matrix
- Native Files / Settings Touched
- Installation Order
- JS Wrapper Boundary
- Verification Matrix
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Compatibility is validated for RN/version/architecture/Hermes/new architecture mode.
- Autolink/manual path selection is explicit and justified.
- iOS/Android native diffs are minimal, scoped, and documented.
- Required native capabilities are declared correctly per platform.
- JS wrapper is typed and isolates native API variability.
- Debug and release verification passes on all target platforms.
- Local and CI verification outcomes are aligned.

## Risks / Rollback
- Risk: native module integration destabilizes build.
  - Rollback: revert scoped native changes and restore last stable dependency set.
- Risk: runtime crashes after integration.
  - Rollback: disable module path and fall back to guarded no-op behavior.
- Risk: integration drift between local and CI blocks release.
  - Rollback: restore CI-aligned dependency/config contract and rerun verification matrix.
