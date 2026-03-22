---
name: mobile-observability-telemetry
description: Execution-grade skill for designing structured mobile telemetry systems with deterministic event schemas, cross-signal correlation, performance instrumentation, and production-ready observability.
metadata:
  domain: mobile-observability-reliability
---

# Skill: mobile-observability-telemetry

## Purpose

Design a production-grade telemetry system for React Native and Expo apps with deterministic schemas, cross-signal correlation, privacy-safe instrumentation, and actionable debugging signals.

## When to Use

- Defining or hardening mobile observability architecture.
- Instrumenting critical user flows for reliability and debugging.
- Standardizing events, logs, metrics, and traces across features.
- Implementing crash/error/performance observability with release-safe signals.
- Designing sampling, offline queueing, and data-quality controls.

## When Not to Use

- Product analytics strategy work without implementation scope.
- Backend-only observability tasks with no mobile client instrumentation.
- Local debug logging tasks not intended for production telemetry.

## Required Inputs

- `target_platform`: `ios` | `android` | `both`
- `app_stack`: `expo` | `react-native-cli` | `hybrid`
- telemetry goals: `debugging` | `analytics` | `performance` | `reliability` | `mixed`
- signal types needed: `events` | `logs` | `metrics` | `traces`
- critical user flows to instrument
- error/crash tracking requirements
- performance monitoring requirements
- telemetry backend and ingestion constraints
- data retention and sampling constraints
- privacy/PII constraints and redaction policy
- offline telemetry behavior and data-loss tolerance
- release criticality
- team ownership and telemetry consumers
- alerting expectations
- cross-signal correlation requirements

## Framework-Specific Directives

- Treat telemetry as a system contract, not scattered logs.
- Enforce iOS/Android schema parity for shared flows.
- Never log sensitive user data, secrets, tokens, or raw payloads.
- Separate debug logs from production telemetry channels.
- Treat performance instrumentation as first-class.
- Ensure telemetry coverage for foreground, background, and edge lifecycle states.
- Avoid coupling telemetry emission to render loops or unstable UI timing.
- Treat missing telemetry in critical paths as system failure.
- Platform lifecycle constraints:
  - Account for iOS background execution limits.
  - Account for Android process-kill unpredictability.
  - Do not assume flush completion on app exit.
- Expo Managed / Expo + EAS:
  - Use release/profile tags consistently across environments.
  - Keep environment isolation strict for keys, routing, and dashboards.
- Bare React Native / hybrid:
  - Correlate native and JS signals via shared session/trace identifiers.
  - Validate crash and performance instrumentation at native + JS boundaries.

## Provider-Specific Directives

- Sentry:
  - Standardize tags: `environment`, `release`, `session_id`, `feature_area`, `error_class`.
  - Distinguish handled vs unhandled exceptions.
- Datadog:
  - Enforce fixed attribute keys and bounded cardinality.
  - Align mobile trace naming with backend service conventions.
- OpenTelemetry:
  - Use semantic conventions and propagate `traceparent` when supported.
  - Keep exporters and batch processors bounded.
- Custom ingestion:
  - Enforce schema versioning and server-side validation.
  - Require idempotency keys for retry-safe ingestion.

## Technical Implementation Patterns

- Telemetry taxonomy:
  - Define boundaries for events, logs, metrics, traces.
  - Assign ownership and intended consumers per signal class.
- Telemetry lifecycle model:
  - Define explicit stages: initialize -> emit -> buffer -> flush -> ingest -> validate -> store.
  - Define ownership and guarantees per stage.
  - Disallow implicit lifecycle transitions.
- Session initialization contract:
  - Ensure `session_id` and correlation identifiers exist before emitting critical signals.
  - Define fallback behavior when session context is not ready.
- Flush ownership model:
  - Define flush triggers: app background, interval, critical event.
  - Keep flush duration bounded with explicit retry policy.
- Event schema design:
  - Enforce stable naming, explicit version, required fields, and validation rules.
  - Reject malformed or incomplete events at client boundary.
- Schema versioning rules:
  - Require explicit `event_version` on every event type.
  - Enforce backward compatibility policy or strict rejection policy.
  - Prevent silent schema drift across releases.
- Data integrity enforcement:
  - Reject or quarantine malformed events.
  - Enforce required field presence before enqueue.
- Telemetry immutability:
  - Do not mutate events after enqueue.
  - Enforce append-only event handling.
- Correlation model:
  - Link signals using `session_id`, `user_id_policy`, `request_id`, `trace_id`, `release`.
  - Preserve backend correlation identifiers on response.
- Flow instrumentation:
  - Instrument `start -> step -> success/failure` for critical paths.
  - Emit explicit terminal outcomes for every critical flow.
- Error classification:
  - Classify as user error, system error, dependency failure, infra failure.
  - Map class to severity, retryability, and alert routing.
- Performance instrumentation:
  - Track startup, screen load, interaction latency, API/network timing.
  - Define per-metric units, sampling, and thresholds.
- Sampling strategy:
  - Always capture critical failures.
  - Sample high-volume low-severity signals deterministically by policy.
- Offline telemetry handling:
  - Queue durably with bounded capacity and ordered flush policy.
  - Use idempotency keys to prevent duplicate ingestion after retries.
- Replay safety:
  - Ensure retried or buffered events do not emit duplicate terminal signals.
  - Preserve single terminal outcome per correlated flow.
- Idempotent ingestion contract:
  - Use `event_id` or idempotency keys for retry-safe ingestion.
  - Deduplicate at client retry boundary and ingest boundary.
- Signal consistency:
  - Ensure identical flows produce identical signal names and required fields.
  - Detect platform divergence for shared flows.
- Critical flow coverage:
  - Require start event for each critical flow.
  - Require terminal success/failure event for each critical flow.
  - Require correlation continuity across critical flow events.
- Telemetry determinism:
  - Identical user flow must produce identical signal sequence under same policy.
  - Identical flow must produce identical schema fields and correlation IDs.
  - Eliminate timing-dependent branching in emission logic.

## Anti-Patterns

- Logging without schema/version ownership.
- Mixing production telemetry with debug console logs.
- Omitting correlation identifiers on critical signals.
- Inconsistent naming for same flow/event class.
- Logging PII/sensitive content directly.
- Emitting duplicate/conflicting terminal signals.
- Assuming strict event order guarantees where none exist.
- Failing to instrument failure paths.
- Ignoring performance signals in reliability-critical flows.
- Treating telemetry gaps as acceptable.

## Decision Tree

- If signal describes discrete user/system action:
  - Emit event.
- If signal describes aggregated rate/latency/health:
  - Emit metric.
- If signal provides diagnostic context for failure path:
  - Emit structured log.
- If signal must correlate client action with backend span:
  - Emit trace/span with propagated IDs.
- If flow is critical:
  - Use full instrumentation with strict schema validation.
- If flow is non-critical high-volume:
  - Apply sampled capture.
- If environment is production:
  - Enforce strict redaction, bounded cardinality, alert-ready fields.
- If environment is non-production:
  - Allow expanded diagnostics in isolated sinks.
- If telemetry send fails:
  - Buffer if within queue budget, else drop with explicit drop signal.
- If duplicate retry candidate detected:
  - Reuse idempotency key and suppress duplicate terminal event.

## Execution Workflow

1. Collect inputs.
2. Define telemetry goals and consumers.
3. Define signal taxonomy.
4. Define event schema and naming.
5. Define correlation strategy.
6. Define instrumentation for key flows.
7. Define error and performance tracking.
8. Define sampling and offline behavior.
9. Define observability signals.
10. Validate deterministic, reproducible, and loss-aware telemetry output across lifecycle stages.
11. Produce structured output.

## Edge Cases

- Telemetry lost in offline state.
- Duplicate events emitted during retry.
- Events emitted before session initialization.
- Missing correlation IDs on partial flows.
- Schema drift between iOS and Android.
- Performance metrics skewed by device thermal/background state.
- User exits before queue flush.
- Crash occurs before telemetry flush.
- Event order mismatch between client and ingestion pipeline.
- Sampling policy removes critical failure signals.
- PII accidentally appears in attributes.
- Background telemetry not flushed before process kill.
- Telemetry emitted before initialization completes.
- Cold-start delays cause missing early signals.
- Notification/deep-link trigger occurs before telemetry bootstrap.
- Crash occurs before first flush.

## Observability

- Track event volume, ingest success, and drop rate.
- Track schema validation failures by event type/version.
- Track missing correlation signal rate.
- Track end-to-end telemetry latency.
- Track crash count vs expected crash telemetry mismatch.
- Track performance regression trends by release.
- Track signal consistency deltas across iOS/Android.
- Track sampling effectiveness and critical-signal retention.
- Track telemetry gaps in critical user flows.
- Detect expected vs actual event sequence mismatch per critical flow.
- Track malformed/rejected events by rejection class.
- Monitor queue saturation and queue-cap drop rate.
- Track flush success/failure rate and repeated flush-failure streaks.
- Measure cold-start delay from app start to first valid signal.
- Track correlation completeness for `session_id` and `trace_id`.
- Never log sensitive payload values.

## Output Contract

- Context Summary
- Assumptions
- Telemetry Goals
- Signal Taxonomy
- Event Schema Design
- Correlation Model
- Instrumentation Plan
- Error / Performance Tracking
- Sampling / Offline Strategy
- Failure Modes
- Data Loss Boundaries
- Observability Signals
- Risks / Rollback
- Next Implementation Step

## Verification Checklist

- Telemetry schema is consistent and versioned.
- Correlation IDs exist across required signals.
- Critical flows are fully instrumented.
- Error and performance signals are captured.
- Telemetry output is deterministic for identical flows.
- PII and sensitive data are not logged.
- Offline queue/flush behavior is bounded and safe.
- Duplicate signal handling is implemented.
- Observability signals cover schema, delivery, and gap failures.

## Risks / Rollback

- Risks:
  - Missing telemetry for critical flows.
  - Inconsistent schemas across platforms.
  - Signal overload or over-aggressive sampling.
  - Privacy boundary violations.
  - Telemetry not matching real runtime behavior.
  - Reduced production-debugging capability.
- Rollback rules:
  - Disable low-value high-volume signal groups first.
  - Tighten sampling and cardinality policies under ingest stress.
  - Hot-disable offending attributes/events violating privacy rules.
  - Revert to previous stable schema version and mapping rules when regression is detected.

## Example

- Scenario:
  - Login flow instrumentation for production app.
  - Steps: `login_start`, `login_submit`, `login_success` or `login_failure`.
- Signal design:
  - Shared fields: `event_version`, `session_id`, `trace_id`, `release`, `environment`.
  - Error path includes `error_class`, `retryable`, `http_status_family`.
  - Performance signal tracks submit-to-response latency.
- Correlation:
  - Reuse same `trace_id` across login event sequence and API span.
- Observability usage:
  - Alert on login failure spike, crash mismatch, and missing `login_terminal` events.
