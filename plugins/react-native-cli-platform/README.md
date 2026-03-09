# React Native CLI Platform Plugin

Senior bare React Native CLI pack for native-heavy projects.

## Scope

Use this pack for JS/native boundary diagnosis, native module integration, release signing, and performance profiling.

## Skills

1. `react-native-cli-platform-assistant`
Primary CLI orchestrator for architecture and multi-layer troubleshooting.

2. `rn-native-module-integration`
Native SDK/module integration with deterministic setup order and typed wrappers.

3. `rn-build-signing`
Release build signing, CI secret handling, store artifact readiness.

4. `rn-performance-profiling`
Measured performance diagnosis and optimization verification.

## Typical Senior Use Cases

- Resolve iOS/Android native build failures with root-cause isolation.
- Introduce native SDKs safely without long-term config drift.
- Standardize release signing between local and CI.
- Build measurable optimization plans for rendering and memory bottlenecks.

## Routing Guidance

- Use this pack for projects with direct `ios/` and `android/` ownership.
- Route delivery/security/data concerns into dedicated production packs when needed.

## Quality Bar

- Minimal native diff footprint.
- Clear rebuild and verification commands.
- CI-parity and repeatable release procedures.
