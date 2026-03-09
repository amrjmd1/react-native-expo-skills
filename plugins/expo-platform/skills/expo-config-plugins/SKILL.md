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
- Project workflow: managed | prebuild | bare.
- Config file source: `app.json` or `app.config.ts`.
- Target platforms: iOS, Android.
- Required native keys/permissions/capabilities.
- Plugin list and current ordering.
- Expected native side effects.
- Rebuild constraints and CI environment.

## Framework-Specific Directives
- Expo Managed:
  - Prefer `app.config.ts` for typed dynamic config.
  - Keep plugin options explicit and minimal.
- Expo + EAS:
  - Validate env-driven config per profile (`development`, `preview`, `production`).
  - Keep config deterministic across local and CI builds.
- Bare React Native:
  - Use config plugins only when still running prebuild flows.
  - If native files are manually owned, document divergence from generated config.

## Technical Implementation Patterns
- Keep plugin sequence explicit; place conflicting plugins in known precedence order.
- Scope plugin mutation narrowly to required native files/keys.
- Version and type plugin options; avoid untyped dynamic option objects.
- Describe generated native diff expectations before running prebuild.
- Use a single source of truth for environment-conditioned values.

## Anti-Patterns
- Broad plugin side effects that mutate unrelated native config.
- Duplicated or conflicting plugin declarations.
- Implicit plugin ordering assumptions.
- Editing generated native files without documenting prebuild overwrite risk.

## Decision Tree
- If change is runtime-only:
  - Do not modify config/plugins.
- If native capability requires declarative config key:
  - Add key in app config and validate generated native output.
- If multiple plugins touch same key:
  - Resolve by explicit ordering and option scoping.
- If environment-specific value is required:
  - Use typed dynamic config with profile-aware inputs.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Identify target config keys and plugin touchpoints.
3. Define plugin ordering and option contracts.
4. Apply minimal config/plugin changes.
5. Document expected native side effects.
6. Run prebuild/build validation path.
7. Verify iOS/Android outputs and runtime behavior.
8. Produce structured output.

## Edge Cases
- Plugin order regression changes previously working key values.
- Prebuild overwrites manual native patch unexpectedly.
- Missing env var generates invalid config in CI.
- Platform-specific config key applied to wrong platform.

## Observability
- Log config resolution source (`env`, default, static).
- Track prebuild/plugin failures by plugin name and stage.
- Capture build-time config validation errors with platform context.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Frontmatter and plugin names are valid.
- Plugin order is explicit and justified.
- iOS/Android config outputs match intended native behavior.
- Environment-specific values resolve deterministically.
- Rebuild/prebuild requirements are stated.

## Risks / Rollback
- Risk: plugin change breaks native build.
  - Rollback: revert plugin delta and restore previous stable order/options.
- Risk: env-conditioned config diverges across environments.
  - Rollback: pin static fallback values and re-run profile validation.
