# Skill Routing Guide

This file maps real-world engineering problems to the most relevant skills.

Start here if you are unsure which skill to use.
Match your problem to the closest description below.
If unclear, start with a general category or escalate to the orchestrator.

Use it for:

- fast AI agent skill selection
- problem-first engineer discovery
- deterministic multi-skill routing when issues span domains

---

## General / Unknown Issues

- Problem unclear or spans multiple domains
  → `mobile-platform-orchestrator`
- Not sure where to start
  → `mobile-platform-orchestrator`

---

## Performance & UI Issues

- General performance debugging
  → `rn-performance-profiling`
- FlatList scroll jank
  → `rn-list-performance-control`
- SectionList or FlashList performance problems
  → `rn-list-performance-control`
- Duplicate pagination fetches
  → `rn-list-performance-control`
- Render churn in list-heavy screens
  → `rn-list-performance-control`
- Animation frame drops
  → `rn-animations-reanimated`
- Gesture interaction stutter
  → `rn-animations-reanimated`
- Transition glitches or interrupted animations
  → `rn-animations-reanimated`
- UI responsiveness issues
  → `rn-performance-profiling`
- Startup performance issues
  → `rn-performance-profiling`
- Memory pressure or runtime jank
  → `rn-performance-profiling`

---

## State & Navigation

- State becoming hard to manage
  → `rn-state-management-architecture`
- Too much global state
  → `rn-state-management-architecture`
- Server state mixed with UI state
  → `rn-state-management-architecture`
- Cross-screen state bugs
  → `rn-state-management-architecture`
- Navigation flow complexity
  → `rn-navigation-architecture`
- Auth gating across routes
  → `rn-navigation-architecture`
- Deep link failures
  → `rn-navigation-architecture`
- Nested navigator structure is breaking down
  → `rn-navigation-architecture`
- Back-stack behavior is inconsistent
  → `rn-navigation-architecture`

---

## Forms & Input Handling

- Form validation inconsistencies
  → `rn-forms-validation`
- Async validation is unreliable
  → `rn-forms-validation`
- Server errors are hard to map to fields
  → `rn-forms-validation`
- Double-submit or duplicate submission bugs
  → `rn-forms-validation`
- Multi-step form architecture is getting brittle
  → `rn-forms-validation`

---

## Authentication & Security

- General authentication or session issues
  → `mobile-auth-session-security`
- Session expires unexpectedly
  → `mobile-auth-session-security`
- Token refresh races
  → `mobile-auth-session-security`
- Logout or revocation behavior is inconsistent
  → `mobile-auth-session-security`
- Multi-device session behavior is unclear
  → `mobile-auth-session-security`
- Secure credential storage
  → `mobile-biometric-secure-storage`
- Biometric re-auth for sensitive actions
  → `mobile-biometric-secure-storage`
- Tokens break after app reinstall or biometric changes
  → `mobile-biometric-secure-storage`
- Local secret handling needs hardening
  → `mobile-biometric-secure-storage`

---

## Data & Networking

- General API or data-layer issues
  → `mobile-api-query-architecture`
- API data structure issues
  → `mobile-api-query-architecture`
- Cache invalidation is inconsistent
  → `mobile-api-query-architecture`
- Retry storms or stale data bugs
  → `mobile-api-query-architecture`
- Pagination consistency issues
  → `mobile-api-query-architecture`
- Offline data conflicts
  → `mobile-offline-sync-conflicts`
- Queued writes fail after reconnect
  → `mobile-offline-sync-conflicts`
- Optimistic updates break during replay
  → `mobile-offline-sync-conflicts`
- Realtime connection instability
  → `mobile-realtime-websocket`
- Websocket reconnect loops
  → `mobile-realtime-websocket`
- Event ordering or deduplication problems
  → `mobile-realtime-websocket`

---

## Expo & Platform

- Expo project triage is unclear
  → `expo-platform-assistant`
- Config plugin issues
  → `expo-config-plugins`
- `app.json` or `app.config.ts` changes affect native output
  → `expo-config-plugins`
- Permission handling issues
  → `expo-device-permissions`
- OTA update issues
  → `expo-updates-ota`
- Runtime version compatibility concerns
  → `expo-updates-ota`
- Build or release problems in Expo
  → `expo-eas-release`
- EAS profile design or rollout planning
  → `expo-eas-release`
- Bare React Native CLI platform triage is unclear
  → `react-native-cli-platform-assistant`
- Native module or SDK integration issues
  → `rn-native-module-integration`
- iOS or Android signing problems
  → `rn-build-signing`
- Monorepo or package boundary problems
  → `mobile-monorepo-package-architecture`

---

## Observability & Reliability

- Missing telemetry
  → `mobile-observability-telemetry`
- Logging and event schemas are inconsistent
  → `mobile-observability-telemetry`
- Crash investigation needs better instrumentation
  → `mobile-observability-telemetry`
- Alert noise is too high
  → `mobile-alerting-incident-response`
- Incident response is unclear
  → `mobile-alerting-incident-response`
- Production mobile alerts need better severity rules
  → `mobile-alerting-incident-response`

---

## Delivery & CI/CD

- CI/CD pipeline design
  → `mobile-cicd-workflows`
- Release gates are weak or inconsistent
  → `mobile-cicd-workflows`
- Signing and secrets need CI alignment
  → `mobile-cicd-workflows`
  → `rn-build-signing`
- React Native or Expo upgrade planning
  → `mobile-migration-upgrades`
- Major SDK migration risk assessment
  → `mobile-migration-upgrades`

---

## UX & Quality

- Accessibility issues
  → `mobile-accessibility-i18n`
- Dynamic type or RTL problems
  → `mobile-accessibility-i18n`
- Localization workflow problems
  → `mobile-accessibility-i18n`
- Background task reliability issues
  → `mobile-background-notifications`
- Push notification delivery issues
  → `mobile-background-notifications`
- Test strategy is weak
  → `rn-testing-quality-gates`
- CI test flakes are slowing delivery
  → `rn-testing-quality-gates`

---

## Revenue Systems

- Subscription purchase flow issues
  → `mobile-payments-subscriptions`
- Entitlement drift between client and server
  → `mobile-payments-subscriptions`
- Receipt validation or restore flow problems
  → `mobile-payments-subscriptions`
- Grace period or billing retry handling
  → `mobile-payments-subscriptions`

---

## Advanced Routing Patterns

### Multi-skill routing

Use multiple skills when the issue cannot be isolated to a single domain.

- Complex performance issue
  → `rn-performance-profiling`
  → `rn-list-performance-control`
- Animation-heavy screen performance issues
  → `rn-performance-profiling`
  → `rn-animations-reanimated`
- Release issue with OTA implications
  → `expo-eas-release`
  → `expo-updates-ota`

### Sequential workflows

Use ordered routing when one skill produces inputs required by the next.

- New feature with complex UI flow
  → `rn-state-management-architecture`
  → `rn-navigation-architecture`
  → `rn-forms-validation`
- New mobile module with platform setup
  → `react-native-cli-platform-assistant`
  → `rn-native-module-integration`
  → `rn-build-signing`
- Expo feature with native config impact
  → `expo-platform-assistant`
  → `expo-config-plugins`
  → `expo-device-permissions`

### Cross-domain flows

Use cross-domain routing when the issue spans multiple architectural layers.

- Auth + offline sync issue
  → `mobile-auth-session-security`
  → `mobile-offline-sync-conflicts`
- Payments + session state issue
  → `mobile-payments-subscriptions`
  → `mobile-auth-session-security`
- Background notifications + telemetry gap
  → `mobile-background-notifications`
  → `mobile-observability-telemetry`
- Monorepo migration affecting feature boundaries
  → `mobile-monorepo-package-architecture`
  → `rn-state-management-architecture`
- Performance issue caused by data sync
  → `mobile-offline-sync-conflicts`
  → `rn-performance-profiling`

---

## Usage Notes

- Start with the closest matching problem.
- Prefer the most specific skill over a broad platform assistant.
- Use multiple skills when the issue spans domains.
- Use ordered routing when one skill depends on outputs from another.
- When in doubt, start broad and then narrow down to a specific skill.
- Escalate to `mobile-platform-orchestrator` only when the domain is unclear or the task spans many domains at once.
