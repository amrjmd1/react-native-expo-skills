---
name: rn-build-signing
description: Execution-grade skill for deterministic React Native CLI build signing, release artifact generation, and store readiness verification.
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
- Target platform (`ios`, `android`, `both`).
- Release type/environment and artifact target.
- Signing asset ownership (certificates, profiles, keystores).
- Bundle/application identifiers and versioning policy.
- CI secret management constraints and current build path.

## Framework-Specific Directives
- RN CLI iOS:
  - Validate certificates, provisioning profiles, team IDs, and export options.
- RN CLI Android:
  - Validate keystore path, alias, passwords, and signingConfig wiring.
- CI-integrated flows:
  - Keep signing secrets externalized and environment-driven.

## Technical Implementation Patterns
- Define a signing contract per platform and environment.
- Validate identifiers/signing assets before running release builds.
- Separate debug and release signing paths explicitly.
- Generate artifact provenance (build variant, commit SHA, checksum).
- Gate submit readiness on signing + metadata validation.

## Anti-Patterns
- Committing secrets or keystores into repository.
- Reusing one signing setup across incompatible environments.
- Skipping pre-submit metadata and permission checks.

## Decision Tree
- If signing assets are invalid/missing:
  - stop and repair signing state before build.
- If build passes but artifact verification fails:
  - block submission and regenerate artifact.
- If iOS/Android differ:
  - resolve per-platform signing independently.

## Execution Workflow
1. Collect required inputs.
2. Validate signing asset ownership and validity.
3. Verify platform signing configuration.
4. Execute release build path with deterministic env inputs.
5. Verify artifact provenance and integrity.
6. Run submit readiness checks.
7. Produce structured output.

## Edge Cases
- iOS succeeds while Android keystore config fails.
- CI secrets differ from local environment assumptions.
- Identifier mismatch appears only at submission stage.
- Certificate/profile valid locally but expired in CI context.

## Observability
- Track build/signing failures by platform and stage.
- Track artifact verification failures.
- Track submission readiness gate failures.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Signing configs are valid and environment-scoped.
- Release artifacts are reproducible and verified.
- CI and local paths use consistent signing contracts.
- Store readiness checks pass before submission.

## Risks / Rollback
- Risk: signing misconfiguration blocks release.
  - Rollback: restore last known-good signing contract and rebuild.
- Risk: incorrect artifact promoted.
  - Rollback: revoke promotion and regenerate verified artifact.
