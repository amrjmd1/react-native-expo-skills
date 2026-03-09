# Expo Platform Plugin

Senior Expo-first implementation pack for production mobile teams.

## Scope

Use this pack for managed-workflow architecture, config correctness, OTA strategy, permissions, and EAS release operations.

## Skills

1. `expo-platform-assistant`
Primary Expo orchestrator for architecture and cross-domain delivery decisions.

2. `expo-eas-release`
Release engineering for EAS Build/Submit, rollout policy, and rollback readiness.

3. `expo-config-plugins`
Typed app config and config-plugin design with prebuild/native side-effect awareness.

4. `expo-updates-ota`
Channel/branch/runtime strategy for safe OTA release operations.

5. `expo-device-permissions`
Permission declarations, runtime request strategy, denied-state UX handling.

## Typical Senior Use Cases

- Stabilize release flow across preview/staging/production channels.
- Diagnose config plugin conflicts and prebuild drift.
- Design OTA strategy with strict binary compatibility controls.
- Harden permission UX against denial and platform edge cases.

## Routing Guidance

- Use this pack as default for Expo projects.
- Route security/data/reliability concerns into dedicated production packs when deeper controls are required.

## Quality Bar

- Strict TypeScript contracts.
- Explicit managed vs native boundary decisions.
- Production-safe defaults and validation-first rollout guidance.
