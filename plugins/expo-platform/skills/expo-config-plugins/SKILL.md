---
name: expo-config-plugins
description: Execution-grade skill for deterministic Expo config plugin design, ordering, and native side-effect control in managed and prebuild workflows.
metadata:
  domain: expo-platform
---

# Skill: expo-config-plugins

## Purpose
Design and modify Expo config and config plugin behavior safely, with explicit native side effects, reproducible prebuild outcomes, and controlled plugin ordering.

## When to Use
- Editing `app.json` or `app.config.ts` with build-impacting changes.
- Adding/removing/reordering config plugins.
- Debugging native changes generated from config during prebuild.
- Hardening permissions and native key declarations from config.

## When Not to Use
- Runtime-only JavaScript logic with no config/native impact.
- Bare-native direct edits that intentionally bypass Expo config generation.

## Required Inputs
- Workflow mode: `managed` | `prebuild` | `bare-with-prebuild`.
- Config source: `app.json` | `app.config.js` | `app.config.ts`.
- Target platforms: `ios` | `android` | `both`.
- EAS profiles involved (`development`, `preview`, `production`, custom).
- Environment variables used during config resolution (with secret/non-secret classification).
- Native capabilities being changed (permissions, entitlements, manifest/plist keys, build properties).
- Current plugin list and exact ordering.
- Plugins touching the same native keys/files.
- Expected generated native files impacted by each plugin.
- CI vs local build differences (env injection, profile, node version, prebuild path).
- Whether manual native patches already exist in generated directories.

## Framework-Specific Directives
- Expo Managed:
  - Prefer static `app.json` when values do not depend on environment/profile.
  - Use `app.config.ts` only when typed dynamic resolution is required.
  - Keep plugin options explicit, minimal, and serializable.
- Expo + EAS:
  - Resolve config per EAS profile with deterministic env contracts.
  - Keep local and CI config parity by pinning profile-aware defaults and validation guards.
  - Fail fast when required env vars are missing rather than silently falling back.
- Bare React Native (with prebuild):
  - Use plugins only for generated native surfaces still owned by prebuild.
  - Treat generated native files as disposable; do not rely on manual edits surviving regeneration.
  - If manual native ownership exists, document boundary and exclude conflicting plugin mutations.

## Technical Implementation Patterns
- Typed dynamic config boundary:
  - Keep all dynamic branches in typed helpers inside `app.config.ts`; avoid ad-hoc inline branching.
- Profile-aware config resolution:
  - Resolve config using explicit `(profile, platform, env)` inputs; no implicit machine-state reads.
- Plugin ordering contract:
  - Declare precedence for plugins mutating shared keys/files; document why order is required.
- Native side-effect mapping:
  - Maintain map: `plugin -> touched file/key -> intended value -> platform scope`.
- Config change classification:
  - Mark each change as `runtime-safe` or `native-regenerating` before implementation.
- Plugin option normalization:
  - Normalize defaults and validate option schema before plugin execution.
- Generated diff expectation review:
  - Define expected native diff surface before prebuild and treat extra diffs as failures until explained.
- Prebuild validation strategy:
  - Run deterministic prebuild/build checks per target profile/platform and compare output against side-effect map.

## Anti-Patterns
- Untyped dynamic config logic with unchecked `process.env` usage.
- Hidden environment-dependent branches that differ between local and CI.
- Relying on implicit plugin order for shared key mutation.
- Plugin code mutating unrelated native files/keys outside declared scope.
- Editing generated files without documenting overwrite risk and ownership boundary.
- Mixing runtime feature flags with build-time native config keys.
- Introducing config side effects that differ across local vs CI execution.
- Duplicated or conflicting plugin declarations without conflict resolution rules.

## Decision Tree
- If values are static across env/profile:
  - Use static config (`app.json`) and avoid dynamic logic.
- If env/profile/platform influences values:
  - Use typed dynamic config (`app.config.ts`) with explicit validation.
- If change is managed-only and non-native-regenerating:
  - Apply config update and run lightweight verification.
- If change is native-regenerating (prebuild-impacting):
  - Map side effects and run full prebuild/build verification path.
- If one key/file has single-plugin ownership:
  - Keep mutation isolated in that plugin.
- If multiple plugins mutate same key/file:
  - Define explicit owner/precedence and normalize options to prevent drift.
- If CI resolution can differ from local:
  - Validate using profile-specific CI-like env inputs before merge.
- If native patch is required repeatedly:
  - Implement or extend plugin mutation; avoid manual persistent patching.
- If key mutation is platform-specific:
  - Scope mutation to platform and assert other platform remains unchanged.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Classify change as `runtime-safe` or `native-regenerating`.
3. Identify config source and ownership boundary.
4. Map plugins to touched native files/keys.
5. Define plugin ordering and option contracts.
6. Define expected generated native side effects.
7. Validate environment/profile resolution rules.
8. Run prebuild/build verification path per platform/profile.
9. Compare generated outputs against expected side-effect map.
10. Produce structured output.

## Edge Cases
- Plugin order regression changes previously working key values.
- Two plugins mutate the same permission/plist/manifest entry.
- Prebuild overwrites manual native patch unexpectedly.
- Missing env var generates invalid config in CI.
- Platform-specific config key applied to wrong platform.
- Dynamic config branches diverge across EAS profiles.
- Plugin option schema changes after package upgrade.
- Generated native diff surface is larger than intended.
- `app.config.ts` reads unstable local machine state.
- Config works locally but fails in EAS build.

## Observability
- Log config resolution source (`static`, `env`, `profile`) without secrets.
- Log plugin execution ordering during prebuild.
- Capture config validation failures with platform/profile context.
- Detect unexpected generated native diffs in CI.
- Surface which plugin last touched conflicting native keys/files.

## Output Contract
- Context Summary
- Assumptions
- Config Ownership Model
- Plugin Ordering Plan
- Native Side-Effect Map
- Environment/Profile Resolution Rules
- Verification Plan
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Config ownership (static vs dynamic) is explicit and justified.
- Plugin ordering is explicit with precedence for shared-key mutations.
- Side-effect map matches generated iOS/Android outputs.
- Env/profile resolution is deterministic across local and CI.
- Required env vars are validated and missing vars fail fast.
- Prebuild overwrite risks for generated files are documented.
- Rebuild/prebuild requirements are stated per impacted platform/profile.

## Risks / Rollback
- Risk: plugin change breaks native build.
  - Rollback: revert plugin delta and restore previous stable order/options.
- Risk: env-conditioned config diverges across environments.
  - Rollback: pin static fallback values and re-run profile validation.
- Risk: conflicting plugin key ownership causes nondeterministic output.
  - Rollback: enforce single-owner plugin precedence and remove conflicting mutation path.

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
