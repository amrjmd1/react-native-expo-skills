# Skills Index

This index lists all available skills grouped by domain.
Use it to quickly discover what each skill does.
Use this index when you already know the system or domain you need.
For problem-based selection, see [docs/SKILL_ROUTING.md](docs/SKILL_ROUTING.md).

## expo-platform
Expo workflow control, config safety, release operations, and OTA/runtime management.

| Skill | Description | Path |
|------|-------------|------|
| expo-platform-assistant | Routes Expo platform problems into the correct config, release, OTA, or permissions path. | `plugins/expo-platform/skills/expo-platform-assistant` |
| expo-eas-release | Controls EAS build, submit, rollout, and rollback workflows for Expo releases. | `plugins/expo-platform/skills/expo-eas-release` |
| expo-config-plugins | Designs config plugin ordering and native side effects for reproducible Expo builds. | `plugins/expo-platform/skills/expo-config-plugins` |
| expo-updates-ota | Controls OTA rollout, runtime compatibility, and rollback safety for Expo Updates. | `plugins/expo-platform/skills/expo-updates-ota` |
| expo-device-permissions | Defines permission declarations, runtime requests, and denied-state handling for Expo apps. | `plugins/expo-platform/skills/expo-device-permissions` |

## mobile-advanced-controls
Deep UI architecture, performance control, and interaction systems.

| Skill | Description | Path |
|------|-------------|------|
| rn-state-management-architecture | Designs state ownership boundaries across UI, feature, session, and server layers. | `plugins/mobile-advanced-controls/skills/rn-state-management-architecture` |
| rn-navigation-architecture | Controls navigation hierarchy, route ownership, auth gating, and deep link behavior. | `plugins/mobile-advanced-controls/skills/rn-navigation-architecture` |
| rn-forms-validation | Structures validation, submission flow, and server error handling for complex forms. | `plugins/mobile-advanced-controls/skills/rn-forms-validation` |
| rn-animations-reanimated | Controls Reanimated worklets, thread boundaries, and interaction-safe motion behavior. | `plugins/mobile-advanced-controls/skills/rn-animations-reanimated` |
| rn-testing-quality-gates | Defines test layers, CI gates, and flake controls for critical React Native flows. | `plugins/mobile-advanced-controls/skills/rn-testing-quality-gates` |
| rn-list-performance-control | Controls virtualization, render churn, and pagination behavior in large lists. | `plugins/mobile-advanced-controls/skills/rn-list-performance-control` |

## mobile-data-platform
Remote data architecture, offline consistency, and realtime transport behavior.

| Skill | Description | Path |
|------|-------------|------|
| mobile-api-query-architecture | Designs query, mutation, caching, and retry behavior for mobile API layers. | `plugins/mobile-data-platform/skills/mobile-api-query-architecture` |
| mobile-offline-sync-conflicts | Resolves offline replay, conflict handling, and mutation recovery under unstable networks. | `plugins/mobile-data-platform/skills/mobile-offline-sync-conflicts` |
| mobile-realtime-websocket | Stabilizes reconnect, ordering, deduplication, and backpressure in realtime channels. | `plugins/mobile-data-platform/skills/mobile-realtime-websocket` |

## mobile-delivery-lifecycle
Release automation, pipeline governance, and migration execution.

| Skill | Description | Path |
|------|-------------|------|
| mobile-cicd-workflows | Designs CI/CD pipelines, release gates, and secret handling for mobile delivery. | `plugins/mobile-delivery-lifecycle/skills/mobile-cicd-workflows` |
| mobile-migration-upgrades | Plans React Native and Expo upgrades with staged execution and rollback controls. | `plugins/mobile-delivery-lifecycle/skills/mobile-migration-upgrades` |

## mobile-identity-security
Authentication lifecycle, secret storage, and device trust controls.

| Skill | Description | Path |
|------|-------------|------|
| mobile-auth-session-security | Designs token lifecycle, refresh safety, revocation, and session integrity on mobile clients. | `plugins/mobile-identity-security/skills/mobile-auth-session-security` |
| mobile-biometric-secure-storage | Hardens local secret storage, biometric re-auth, and recovery after device trust changes. | `plugins/mobile-identity-security/skills/mobile-biometric-secure-storage` |

## mobile-observability-reliability
Telemetry design, alert quality, and incident response systems.

| Skill | Description | Path |
|------|-------------|------|
| mobile-observability-telemetry | Designs event schemas, crash signals, and privacy-safe telemetry for production apps. | `plugins/mobile-observability-reliability/skills/mobile-observability-telemetry` |
| mobile-alerting-incident-response | Defines alert rules, severity policy, and incident workflows for mobile operations. | `plugins/mobile-observability-reliability/skills/mobile-alerting-incident-response` |

## mobile-platform-architecture
Workspace topology, package boundaries, and internal platform scaling.

| Skill | Description | Path |
|------|-------------|------|
| mobile-monorepo-package-architecture | Defines workspace boundaries, package contracts, and versioning policy across mobile repos. | `plugins/mobile-platform-architecture/skills/mobile-monorepo-package-architecture` |

## mobile-platform-orchestrator
Cross-domain triage and fallback routing across mobile engineering domains.

| Skill | Description | Path |
|------|-------------|------|
| mobile-platform-orchestrator | Routes unclear or multi-domain mobile problems into a safe execution path. | `plugins/mobile-platform-orchestrator/skills/mobile-platform-orchestrator` |

## mobile-revenue-systems
Purchase flows, subscription lifecycle control, and entitlement consistency.

| Skill | Description | Path |
|------|-------------|------|
| mobile-payments-subscriptions | Designs purchase flows, entitlement state, and billing failure recovery for mobile apps. | `plugins/mobile-revenue-systems/skills/mobile-payments-subscriptions` |

## mobile-user-experience-quality
Accessibility, localization, background behavior, and notification reliability.

| Skill | Description | Path |
|------|-------------|------|
| mobile-accessibility-i18n | Defines accessibility semantics, localization flow, RTL safety, and dynamic type behavior. | `plugins/mobile-user-experience-quality/skills/mobile-accessibility-i18n` |
| mobile-background-notifications | Designs background tasks, push delivery, and token lifecycle reliability across mobile platforms. | `plugins/mobile-user-experience-quality/skills/mobile-background-notifications` |

## react-native-cli-platform
Bare React Native platform triage, native integration, signing, and profiling.

| Skill | Description | Path |
|------|-------------|------|
| react-native-cli-platform-assistant | Routes RN CLI platform issues into native integration, signing, or profiling paths. | `plugins/react-native-cli-platform/skills/react-native-cli-platform-assistant` |
| rn-native-module-integration | Integrates native SDKs and modules with minimal drift and repeatable verification. | `plugins/react-native-cli-platform/skills/rn-native-module-integration` |
| rn-build-signing | Produces signed iOS and Android release artifacts with CI-safe secret handling. | `plugins/react-native-cli-platform/skills/rn-build-signing` |
| rn-performance-profiling | Diagnoses performance bottlenecks across startup, rendering, memory, and thread contention. | `plugins/react-native-cli-platform/skills/rn-performance-profiling` |
