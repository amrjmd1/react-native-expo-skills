---
name: mobile-cicd-workflows
description: Execution-grade skill for designing deterministic mobile CI/CD pipelines with reproducible builds, release gates, artifact promotion, and rollback-ready delivery systems.
metadata:
  domain: mobile-delivery-lifecycle
---

# Skill: mobile-cicd-workflows

## Purpose
Design deterministic mobile CI/CD systems for React Native and Expo apps with explicit stage ownership, reproducible artifacts, release safety, and rollback readiness.

## When to Use
- Designing or hardening CI/CD pipelines for mobile apps.
- Defining merge/release gates and promotion rules.
- Fixing reproducibility, signing, environment, or release drift.
- Establishing artifact provenance and rollback reliability.

## When Not to Use
- Local-only developer scripting with no CI/CD scope.
- One-off deployment instructions without pipeline architecture.
- Backend-only delivery systems unrelated to mobile artifacts.

## Required Inputs
- Target platform: `ios` | `android` | `both`.
- App stack: `expo` | `react-native-cli` | `hybrid`.
- Build system: `eas` | `fastlane` | `xcodebuild` | `gradle` | `hybrid`.
- CI provider and runner constraints.
- Environment model: `dev` | `staging` | `production`.
- Release type: `internal` | `beta` | `production`.
- Branch/promotion model.
- Artifact types: `apk` | `aab` | `ipa` | JS bundle/OTA metadata (if applicable).
- Signing ownership model.
- Secret management model.
- Merge gate expectations.
- Release gate expectations.
- Rollback expectations.
- Release frequency and cadence.
- Team/operator ownership and on-call model.
- Store submission requirements.
- Compliance/security requirements.

## Framework-Specific Directives
- Expo + EAS workflows:
  - Keep profile-specific contracts explicit for build, update, submit, and promotion stages.
  - Keep OTA and binary release rules explicit and separate when both are active.
  - Enforce environment isolation per EAS profile/channel.
- React Native CLI native build workflows:
  - Treat iOS and Android release contracts as first-class and independently verifiable.
  - Keep native signing/build inputs deterministic and CI-owned.
- Hybrid JS + native delivery:
  - Separate JS delivery path from native artifact path when release contracts differ.
  - Define compatibility gate when OTA and native release sequencing overlaps.
- Cross-context parity:
  - Require local validation assumptions to match CI execution assumptions.
  - Keep promotion rules explicit for same-artifact promotion vs rebuild-per-environment.

## Technical Implementation Patterns
- Pipeline architecture model:
  - Define stages: `validate -> build -> verify -> sign -> promote -> monitor`.
  - Keep deterministic stage boundaries and artifacts passed between stages.
  - Assign explicit owner and SLA per stage.
- Build reproducibility contract:
  - Pin toolchain/runtime versions.
  - Lock dependency graph and install mode.
  - Eliminate local-only assumptions.
  - Require: same commit + same contract => equivalent artifact.
- Artifact lifecycle model:
  - Define creation, verification, signing, promotion, retention, and rollback usage.
  - Prevent ad-hoc rebuilds of same release candidate unless explicitly required.
- Artifact provenance:
  - Require `git_sha`, `build_id`, `release_candidate_id`, `environment`, `platform`, `artifact_hash`.
- Environment isolation model:
  - Separate dev/staging/prod pipelines, variables, and signing paths.
  - Block cross-environment credential or artifact contamination.
- Signing and secret boundary model:
  - Keep signing/secrets external to repo.
  - Define ownership, injection path, expiry checks, and auditability.
- Release gating model:
  - Distinguish merge-blocking gates from release-blocking gates.
  - Define hard block signals vs warning-only observability signals.
- Rollback readiness contract:
  - Ensure previous known-good artifact exists and is compatible.
  - Define trigger, owner, and tested execution path.
- Failure classification model:
  - Classify as `infrastructure`, `flake`, `product regression`, `signing/config`.
  - Route each class to explicit remediation owner.
- Stage promotion model:
  - Define when artifact is promoted unchanged vs rebuilt with explicit contract change.

## Anti-Patterns
- Rebuilding the same release candidate per environment without explicit contract.
- Using mutable artifacts after verification/signing.
- Mixing staging and production secrets/signing paths.
- Relying on local machine state for release correctness.
- Treating reproducibility failures as warning-only.
- Bypassing release-blocking gates informally.
- Using flaky suites as hard blockers without owner/triage policy.
- Promoting artifacts without provenance metadata.
- Shipping without tested rollback path.
- Leaving OTA/native coupling implicit.

## Decision Tree
- If gate type is merge:
  - prioritize fast deterministic feedback and developer velocity.
- If gate type is release:
  - enforce stricter confidence, provenance, and signing thresholds.
- If failure is infrastructure:
  - retry stage with infra diagnostics; do not reclassify as product failure.
- If failure is flaky gate:
  - quarantine/triage per policy; block release only by policy-defined criticality.
- If failure is product regression:
  - fail-fast and block promotion.
- If release type is internal/beta:
  - allow reduced blast radius with strict provenance.
- If release type is production:
  - require full release-blocking gates and rollback readiness.
- If same artifact can be promoted safely:
  - promote unchanged artifact.
- If environment contract differs:
  - rebuild with explicit contract and new provenance.
- If OTA-safe path is chosen:
  - validate runtime/channel compatibility and sequencing.
- If native rebuild path is required:
  - block OTA-only promotion until native artifact verified.
- If release-blocking failure occurs:
  - choose rollback/hold/re-run based on failure classification and risk.

## Execution Workflow
1. Collect required inputs.
2. Classify release model and platform constraints.
3. Define environment and stage boundaries.
4. Define reproducibility contract.
5. Define artifact lifecycle and provenance requirements.
6. Define merge and release gates.
7. Define secret/signing ownership.
8. Define promotion and rollback rules.
9. Define observability signals and failure classification.
10. Validate pipeline determinism.
11. Produce structured output.

## Edge Cases
- Build succeeds locally but fails in CI.
- iOS and Android diverge under same release candidate.
- Secrets/signing valid in one environment but not another.
- Flaky test blocks release with no triage owner.
- Artifact promoted without matching provenance metadata.
- Rollback target missing or incompatible.
- Environment mismatch causes runtime-only failure.
- CI passes but production artifact crashes due to config drift.
- Parallel pipelines create inconsistent release candidates.
- Store submission blocked by metadata/version mismatch.
- OTA/native sequencing creates incompatible delivery state.
- Release gate passes but monitoring immediately shows regression spike.

## Observability
- Track pipeline success/failure rate by stage.
- Track flaky stage/suite detection trends.
- Track build duration by stage.
- Track artifact verification/signature failures.
- Track provenance mismatch events.
- Track rollback frequency and reasons.
- Track release gate failure trends.
- Track time-to-release from merge to promotion.
- Track store submission failures by platform.
- Track post-promotion regression signals.
- Do not log secrets or sensitive signing material.

## Output Contract
- Context Summary
- Assumptions
- Pipeline Architecture
- Stage Definitions
- Environment Model
- Reproducibility Contract
- Artifact Lifecycle / Provenance Rules
- Merge / Release Gates
- Secret / Signing Strategy
- Promotion / Rollback Plan
- Observability Signals
- Verification Matrix
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Same commit under same contract produces equivalent artifact.
- Stage boundaries and ownership are explicit.
- Artifact provenance is recorded and verifiable.
- Merge and release gates are distinct and enforced.
- Secrets/signing paths are isolated per environment.
- Rollback target exists and is executable.
- CI failures are classifiable by failure model.
- Release promotion path is deterministic.
- Post-release monitoring signals are attributable.

## Risks / Rollback
- Risk: non-reproducible builds.
  - Rollback: freeze promotion, restore pinned build contract, rebuild verified candidate.
- Risk: signing drift.
  - Rollback: revert signing contract to known-good and re-verify signatures.
- Risk: environment drift.
  - Rollback: restore environment contract from baseline and rerun promotion gate.
- Risk: flaky gate inflation.
  - Rollback: quarantine unstable suites with owner/SLA and enforce stable critical blockers.
- Risk: invalid artifact promotion.
  - Rollback: demote artifact and promote last known-good immutable candidate.
- Risk: rollback unavailability.
  - Rollback: hold release, rebuild compatible rollback candidate, and block further promotion.

## Example
- React Native app, `staging` and `production` environments.
- CI flow: validate -> build (`aab`, `ipa`) -> verify/sign -> promote immutable candidate.
- Promotion uses unchanged artifact from staging to production with provenance checks.
- Rollback plan references previous signed production candidate and tested rollback command path.

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
