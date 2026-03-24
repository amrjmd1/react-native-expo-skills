---
name: rn-build-signing
description: Execution-grade skill for deterministic React Native CLI release signing, CI secret handling, and store artifact readiness.
metadata:
  domain: react-native-cli-platform
---

# Skill: rn-build-signing

## Purpose
Produce reproducible signed iOS/Android release artifacts with deterministic signing configuration, CI parity, and submission readiness checks.

## When to Use
- Configuring or fixing iOS/Android signing for release builds.
- Preparing release artifacts for store submission.
- Diagnosing signing failures across local and CI.

## When Not to Use
- Runtime app behavior debugging unrelated to build/signing.
- Development-only debug build workflows.

## Required Inputs
- Target platform: `ios` | `android` | `both`.
- Release type: `internal` | `beta` | `production`.
- Build system path: `local` | `CI` | `hybrid`.
- iOS signing assets: certificates, provisioning profiles, bundle identifiers, team ID.
- Android signing assets: keystore, alias, passwords, `applicationId`.
- Flavor/build-variant structure and release mapping.
- Environment separation rules (dev/staging/prod and signing boundaries).
- Store target: App Store Connect | Google Play | internal distribution.
- Artifact expectations: `.ipa`, `.aab`, `.apk`.
- Secret ownership model and rotation policy.
- Integration criticality: release-blocking vs exploratory.

## Framework-Specific Directives
- React Native CLI default workflow:
  - Keep signing materials out of source control.
  - Keep release signing config explicit and environment-scoped.
- iOS-specific signing path:
  - Validate certificate/profile/team/bundle alignment before archive/export.
  - Treat export options and provisioning mapping as part of signing contract.
- Android-specific signing path:
  - Validate keystore, alias, and release signingConfig per flavor/build variant.
  - Ensure `applicationId` and manifest/store targets are aligned.
- CI-owned signing flow:
  - Inject secrets via CI contract only; never through ad-hoc local state.
  - CI must use the same signing contract expected by release operators.
- Local verification flow:
  - Local builds used for release confidence must validate the same artifact assumptions as CI.
  - Flavor/profile-aware signing paths must match release pipeline intent.

## Technical Implementation Patterns
- Signing asset ownership map:
  - Define owner, source, expiry, and environment scope for each signing asset.
- Environment-to-signing contract:
  - Map environment (`internal/beta/production`) to platform signing inputs and store target.
- Flavor/build-variant signing map:
  - Define deterministic signingConfig per variant; reject implicit fallback behavior.
- CI secret injection contract:
  - Define required secret keys, injection timing, and validation checkpoints.
- Signing configuration isolation:
  - Keep debug and release signing configs isolated and non-overlapping.
- Artifact provenance checks:
  - Stamp platform, variant, git SHA, build ID, and release candidate ID.
- Release artifact verification matrix:
  - Verify signature validity + installability + expected output type per platform/variant.
- Store-readiness gating checklist:
  - Validate signing, identifiers, metadata, manifest/permission compliance before submission.
- Rollback-ready artifact retention:
  - Retain previous known-good signed artifacts with provenance and retrieval path.

## Anti-Patterns
- Committing secrets or keystores into repository.
- Using one signing setup for incompatible environments.
- Relying on local machine state not reproducible in CI.
- Mixing debug and release signing configuration.
- Producing release artifacts without signature verification.
- Allowing flavor/build-variant signing drift.
- Reusing stale credentials without expiry/validity checks.
- Skipping pre-submit metadata and permission checks.

## Decision Tree
- If signing assets are invalid/missing:
  - stop and repair signing state before build.
- If target is iOS-only or Android-only:
  - run platform-specific signing path and verification matrix for that platform only.
- If target is both:
  - require both platform artifacts to pass verification before promotion.
- If build path is local validation:
  - allow confidence checks only when local contract matches CI contract.
- If build path is CI release:
  - treat CI output as authoritative release artifact source.
- If release type is internal:
  - allow internal signing path with reduced distribution scope.
- If release type is beta:
  - enforce beta signing/environment isolation and pre-store checks.
- If release type is production:
  - require full readiness gates and rollback-ready previous artifact.
- If same artifact is promoted across environments:
  - permit only when signing/environment contract is compatible.
- If contract differs by environment:
  - rebuild per environment with correct signing inputs.
- If flavor-specific signing is required:
  - use flavor map; do not fallback to shared default.
- If issue is release-blocking:
  - prioritize deterministic CI remediation and block promotion.
- If issue is exploratory:
  - allow scoped setup validation without promotion.
- If build passes but artifact verification fails:
  - block submission and regenerate artifact.
- If iOS/Android differ:
  - resolve per-platform signing independently.

## Execution Workflow
1. Collect required inputs.
2. Classify target platforms and release type.
3. Identify signing assets and ownership boundaries.
4. Map build variants/flavors to signing configuration.
5. Define CI secret injection and local verification rules.
6. Generate signed artifact(s).
7. Verify signature and artifact readiness.
8. Validate store submission prerequisites.
9. Confirm rollback-ready previous artifact retention.
10. Produce structured output.

## Edge Cases
- iOS archive succeeds but signing/export fails.
- Android release builds locally but CI signing fails.
- Wrong provisioning profile selected for one environment.
- Keystore alias mismatch only in release pipeline.
- One platform artifact is valid while the other fails store-readiness checks.
- Flavor-specific signing config drift causes wrong signature path.
- Expired certificate or stale keystore password blocks release.
- Artifact signs successfully but store metadata/manifest checks fail.
- Local build passes due to developer keychain state but CI fails.

## Observability
- Record build variant, flavor, and platform for each signed artifact.
- Log signing path used (`local`, `CI`, `hybrid`).
- Capture signature verification status per artifact.
- Detect local-vs-CI artifact/signing divergence.
- Surface signing asset expiry or mismatch risk.
- Track artifact provenance (git SHA, build ID, release candidate ID).
- Track submission readiness gate failures.
- Do not log secrets or credential values.

## Output Contract
- Context Summary
- Assumptions
- Platform / Release Matrix
- Signing Asset Ownership
- Variant / Flavor Signing Map
- CI Secret Handling Contract
- Artifact Verification Matrix
- Store Readiness Checks
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Signing configs are valid, release-scoped, and environment-isolated.
- Flavor/build-variant mapping resolves to intended signingConfig.
- Release artifacts are reproducible and signature-verified.
- CI and local paths follow the same signing contract assumptions.
- Artifact provenance fields are present and consistent.
- Store readiness checks pass for each target platform.
- Previous rollback-ready artifact is retained and retrievable.

## Risks / Rollback
- Risk: signing misconfiguration blocks release.
  - Rollback: restore last known-good signing contract and rebuild.
- Risk: incorrect artifact promoted.
  - Rollback: revoke promotion and regenerate verified artifact.

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
