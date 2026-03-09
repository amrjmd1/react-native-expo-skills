---
name: mobile-observability-telemetry
description: Execution-grade skill for designing telemetry, structured logging, and observability instrumentation in React Native and Expo apps.
metadata:
  domain: mobile-observability-reliability
---

# Skill: mobile-observability-telemetry

## Purpose
Design deterministic, privacy-safe mobile telemetry architecture for React Native and Expo apps, including event schemas, error classification, correlation IDs, sampling, batching, and runtime flush behavior.

## When to Use
- Defining or hardening structured telemetry events across app features.
- Standardizing error/crash observability and operational metrics.
- Implementing session and request correlation across client + backend signals.
- Adding batching, retry, and offline-safe event delivery.
- Enforcing privacy-safe logging rules before production release.

## When Not to Use
- Feature analytics strategy discussion without instrumentation implementation scope.
- Backend-only observability decisions with no mobile runtime instrumentation.
- Local debug-only logging with no production telemetry pipeline.

## Required Inputs
Collect these inputs first. Missing inputs must be listed as assumptions.

- Platform mode: `Expo Managed` | `Expo + EAS` | `Bare React Native`.
- Telemetry backend: `Sentry` | `Datadog` | `OpenTelemetry` | custom ingestion.
- Logging strategy: structured logs | event telemetry | hybrid.
- Privacy policy: data classification, PII/PHI rules, redaction policy, retention constraints.
- Session tracking policy: session start/end/timeout rules and ID lifecycle.
- Crash reporting system and required release/build metadata.
- Performance monitoring targets: app start, screen render, API latency, JS frame drops, memory, ANR/freezes.
- Event sampling policy per environment and per event class.
- Environment separation rules: dev/staging/prod keys, routing, and dashboard boundaries.
- Correlation identifiers: `session_id`, `device_id_policy`, `request_id`, `trace_id`, `release`.
- Transport constraints: batch size, max queue size, retry budget, flush triggers.
- Offline behavior requirements: queue persistence, flush on reconnect, data loss tolerance.

## Framework-Specific Directives
### Expo Managed
- Use `@sentry/react-native` with Expo integration for crash/error capture and release tagging.
- Use `expo-application` and `expo-device` for non-sensitive device/build metadata enrichment.
- Use `@react-native-community/netinfo` to gate upload/flush behavior on connectivity state.
- Keep telemetry queue in persistent storage (`expo-sqlite` for durable high-volume queues; avoid large payload queues in `AsyncStorage`).

### Expo + EAS
- Follow Expo Managed directives plus release channel/environment tagging from EAS metadata.
- Separate DSNs/API keys by EAS profile (`development`, `preview`, `production`) with hard fail on key mismatch.
- Ensure source maps/release artifacts align with uploaded build version for symbolicated crashes.
- Apply stricter production sampling defaults in release builds.

### Bare React Native
- Use `@sentry/react-native`, OpenTelemetry SDK, or vendor-native SDKs with native bridge lifecycle validation.
- Use native performance signals where available (startup time, ANR, native crash) and correlate to JS session IDs.
- Store telemetry queue durably (SQLite/Realm) when loss tolerance is low.
- Ensure background flush is best-effort; never assume guaranteed execution after app background/kill.

## Provider-Specific Directives
### Sentry
- Standardize event tags: `environment`, `release`, `build_number`, `session_id`, `feature_area`.
- Use breadcrumbs for bounded context; do not use breadcrumbs as high-volume telemetry stream.
- Distinguish handled vs unhandled exceptions and classify severity explicitly.

### Datadog
- Use structured log attributes with stable keys and bounded cardinality.
- Use trace-resource naming conventions for mobile API spans.
- Enforce client-side sampling for high-volume events before ingestion.

### OpenTelemetry
- Adopt semantic conventions for span/event attributes.
- Propagate `traceparent` across API calls where backend supports distributed tracing.
- Use exporters/batch processors with bounded queues and retry backoff.

### Custom Ingestion
- Define strict JSON schema versioning for every event type.
- Require server-side idempotency key to dedupe client retries.
- Enforce reject/drop policy for malformed or oversized events.

## Technical Implementation Patterns
- Telemetry event schema:
  - Required fields: `event_name`, `event_version`, `timestamp_ms`, `environment`, `release`, `session_id`, `correlation_id`, `severity`, `attributes`.
  - Use versioned schemas; never introduce breaking payload changes without version bump.
- Structured log format:
  - Keep logs machine-parseable (JSON) with fixed key casing and bounded attribute cardinality.
  - Separate telemetry logs from local debug logs by channel and transport.
- Session lifecycle tracking:
  - Emit deterministic `session_start`, `session_heartbeat`, `session_end`.
  - Close stale sessions after inactivity timeout; rotate session IDs on logout/login boundary.
- Error classification system:
  - Classify as `network`, `auth`, `validation`, `runtime`, `native_crash`, `dependency`, `unknown`.
  - Map each class to severity, retryability, and alert routing policy.
- Correlation ID propagation:
  - Generate client correlation context once per session and propagate into every event and network request.
  - Preserve upstream `request_id`/`trace_id` when returned by backend.
- Client-side sampling rules:
  - Always-capture critical errors/crashes; sample low-severity high-volume events by policy.
  - Sampling must be deterministic by environment and event class.
- Event batching and upload:
  - Batch by size/time thresholds with max payload cap and bounded retry budget.
  - Use idempotency keys to avoid duplicate ingestion on retry.
- Background flush strategies:
  - Flush on app background, app terminate signal (best effort), and reconnect.
  - Keep durable overflow queue with drop policy when capacity exceeded.

## Anti-Patterns
- Logging sensitive user data, secrets, tokens, or raw request/response bodies.
- Sending payloads without redaction/classification checks.
- Unbounded event emission without sampling, rate limits, or queue caps.
- Mixing console debug output into production telemetry pipelines.
- Emitting events without `environment`/`release` context.
- Omitting correlation IDs (`session_id`, `request_id`, `trace_id`) on critical events.
- Using high-cardinality attributes (user-entered text) as metric dimensions.
- Treating crash telemetry as reliable if no flush/retry behavior is defined.

## Decision Tree
```text
If environment == production
  -> enable strict redaction, bounded sampling, and alerting-grade error/crash telemetry.
Else
  -> allow higher debug signal density with separate non-prod routing.

If event_volume == high
  -> apply class-based sampling + batching + queue caps.
Else
  -> use near-full capture with bounded retries.

If privacy_mode == strict
  -> disable sensitive fields, hash or redact identifiers, reduce attribute granularity.
Else
  -> allow broader non-sensitive context fields per policy.

If logging_model == centralized
  -> send normalized events to single ingestion schema and enforce schema validation at client boundary.
Else (distributed/vendor split)
  -> define canonical field mapping and correlation strategy across sinks.

If observability_model == client-heavy
  -> prioritize on-device performance/error/session signals and offline queue durability.
Else (server-heavy)
  -> prioritize request trace linkage and backend correlation identifiers.
```

## Execution Workflow
1. Collect required inputs and record assumptions.
2. Classify telemetry goals (debugging, reliability, performance, product signal) by priority.
3. Define event schema (required fields, versioning, validation rules).
4. Define error taxonomy and severity mapping.
5. Define correlation strategy (`session_id`, `request_id`, `trace_id`, release tags).
6. Define sampling policy by environment and event class.
7. Implement instrumentation hooks (app lifecycle, navigation, network, errors, crashes, performance).
8. Implement batching, queue persistence, retry/backoff, and flush triggers.
9. Define observability metrics and alert thresholds.
10. Verify privacy compliance and redaction gates.
11. Produce output using the Output Contract exactly.

## Edge Cases
- Telemetry lost during abrupt app crash before flush.
- Event flood after reconnect drains queue too aggressively.
- Duplicate telemetry emitted after client retry and uncertain ACK.
- Network unavailable during scheduled event flush.
- Privacy mode enabled disables required attributes unexpectedly.
- Device clock skew causes out-of-order timestamps/traces.
- Telemetry queue overflow drops important events.
- App killed during batch upload leaves partial delivery.
- Crash occurs before telemetry SDK initialization completes.
- Environment key misconfiguration routes prod telemetry to staging.
- Session ID rotates incorrectly and breaks trace correlation.

## Observability
Emit bounded, privacy-safe metrics and counters:
- `event_rate`
- `error_rate`
- `crash_rate`
- `session_length`
- `api_failure_rate`
- `replay_failure_rate`
- `offline_queue_depth`

Rules:
- Include `environment` and `release` labels on all metrics/events.
- Keep cardinality bounded (`screen`, `feature_area`, `error_class` only; no raw user text).
- Track queue drops, retry counts, flush latency, and duplicate-reject counts.
- Never emit sensitive payload content unless explicitly redacted.

## Output Contract
Return these sections in order:

1. Context Summary
2. Assumptions
3. Telemetry Goals
4. Event Schema Design
5. Error Classification Strategy
6. Correlation Strategy
7. Sampling Policy
8. Event Transport Strategy
9. Observability Metrics
10. Failure Handling Plan
11. Verification Checklist
12. Risks / Rollback
13. Next Implementation Step

## Verification Checklist
- Event schema has required fields, versioning, and validation rules.
- Error taxonomy maps each class to severity and retryability.
- Correlation IDs are present on telemetry and outbound requests.
- Environment and release tagging is enforced.
- Privacy redaction rules block sensitive fields in all sinks.
- Sampling and rate limits bound event volume in production.
- Queue/batching/retry behavior is deterministic and bounded.
- Crash + app lifecycle flush behavior is implemented and tested.
- Duplicate ingestion protection exists (idempotency/dedupe keys).
- Metrics are actionable, bounded, and dashboard-ready.

## Risks / Rollback
- Risk: over-instrumentation increases cost and noise.
  - Rollback: tighten sampling and disable low-value event groups via remote config.
- Risk: privacy leak through new attributes.
  - Rollback: enforce allowlist schema, hot-disable offending fields, purge sink indexes where possible.
- Risk: queue/batch logic causes telemetry loss.
  - Rollback: reduce batch size, increase flush frequency, fallback to immediate send for critical events.
- Risk: broken release correlation blocks crash symbolication.
  - Rollback: pin release tagging strategy and reprocess mapping uploads before next rollout.

## Example Request
```text
Platform mode: Expo + EAS
Telemetry backend: Sentry + custom ingestion
Logging strategy: hybrid
Privacy policy: strict redaction, no raw payloads
Session tracking: 30-min inactivity timeout
Crash reporting: Sentry
Performance targets: cold start, screen render p95, API latency p95
Sampling policy: prod info=10%, warning=30%, errors=100%
Environment separation: strict dev/staging/prod keys
Correlation IDs: session_id + request_id + trace_id
```

## Example Response Shape
```text
Context Summary
- ...

Assumptions
- ...

Telemetry Goals
- ...

Event Schema Design
- ...

Error Classification Strategy
- ...

Correlation Strategy
- ...

Sampling Policy
- ...

Event Transport Strategy
- ...

Observability Metrics
- ...

Failure Handling Plan
- ...

Verification Checklist
- ...

Risks / Rollback
- ...

Next Implementation Step
- ...
```
