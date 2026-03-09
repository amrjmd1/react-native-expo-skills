---
name: mobile-alerting-incident-response
description: Execution-grade skill for defining operational alerts and incident response workflows for React Native and Expo apps.
metadata:
  domain: mobile-observability-reliability
---

# Skill: mobile-alerting-incident-response

## Purpose
Define deterministic mobile alerting and incident response systems that convert telemetry into actionable alerts, classify incident severity, route escalation, and drive runbook-based mitigation for React Native and Expo apps.

## When to Use
- Designing production alert rules from mobile telemetry signals.
- Hardening on-call escalation and incident response for mobile failures.
- Reducing false positives/alert storms while preserving critical detection.
- Introducing runbook-linked alert handling and incident lifecycle definitions.
- Standardizing SEV1-SEV4 classification for app reliability incidents.

## When Not to Use
- Feature analytics dashboards with no operational alerting action.
- Backend-only incident workflows with no mobile app impact path.
- One-off debugging tasks that do not need durable alert policy.

## Required Inputs
Collect these before implementation; missing values must be explicit assumptions.

- Telemetry backend: `Sentry` | `Datadog` | `OpenTelemetry` | custom.
- Alerting platform: `PagerDuty` | `OpsGenie` | Slack alerts | custom.
- Environment scope: `dev`, `staging`, `prod` with routing boundaries.
- Service ownership model: team ownership by feature/domain/platform.
- On-call rotation model: primary/secondary, timezone model, handoff rules.
- Severity taxonomy: `SEV1`-`SEV4` with user-impact definitions.
- Escalation policy: ack timeout, re-page policy, manager escalation, incident commander criteria.
- Alert noise tolerance: expected daily alert budget and false-positive budget.
- Mobile platform coverage: iOS, Android, app version cohort, release channel.
- Telemetry signals available: crash rate, ANR/freeze, API failure, auth error bursts, latency p95/p99, offline replay failures.
- Release context: rollout strategy, canary cohorts, kill switch capability.
- Telemetry ingestion delay tolerance and fallback signal strategy.

## Framework-Specific Directives
### Expo Managed
- Use `@sentry/react-native` or equivalent managed-compatible telemetry SDK for crash/error signals.
- Include Expo release/channel metadata in alerts to isolate rollout-specific incidents.
- Account for limited background execution: alert rules should not assume guaranteed immediate client flush.
- Treat ingestion delay as first-class condition; pair client metrics with backend API health signals.

### Expo + EAS
- Split alert routing by EAS environment/profile to prevent non-prod pages to prod on-call.
- Tie crash/performance alerts to release/build identifiers for rapid rollback targeting.
- Add rollout-window guards (first N minutes/hours post-release) with tighter thresholds for crash/ANR.
- Validate source-map/symbolication pipeline; unsymbolicated spikes require separate operational alert.

### Bare React Native
- Include native crash and ANR/freeze signals from platform SDKs.
- Add device/OS/version segmentation in alert diagnostics to localize platform-specific regressions.
- Use durable local queue health (`offline_queue_depth`, replay failure rates) as alertable signals.
- Distinguish JS runtime errors from native faults in severity mapping.

## Provider-Specific Directives
### Sentry
- Alert on crash-free sessions/users degradation and fatal exception spikes.
- Use issue fingerprinting and grouping controls to reduce duplicate paging.
- Route high-volume non-fatal errors to ticket workflow unless threshold crosses SEV policy.

### Datadog
- Use monitor types by signal: metric threshold, anomaly, and composite monitors.
- Enforce cardinality-safe tags for grouping (`env`, `platform`, `release`, `feature_area`).
- Add mute/maintenance windows during planned migrations.

### OpenTelemetry / Custom
- Convert spans/events to derived operational metrics before paging.
- Require dedupe keys and incident grouping fields at ingestion.
- Validate end-to-end latency of telemetry pipeline; stale data should suppress paging or downgrade severity.

## Technical Implementation Patterns
- Alert signal classification:
  - Classify as `availability`, `stability`, `performance`, `data-integrity`, `dependency`.
  - Map each class to default severity floor and escalation target.
- Alert rule definitions:
  - Define rule ID, source metric, comparator, threshold, window, min sample size, cooldown, dedupe key, severity, runbook URL.
  - Keep rules versioned and environment-scoped.
- Threshold-based alerts:
  - Use explicit static thresholds for critical signals (`crash_rate`, `api_failure_rate`, `auth_error_rate`).
  - Require minimum traffic gate to avoid low-volume false positives.
- Rate-based alerts:
  - Alert on sustained error rates over rolling windows (e.g., 5m, 15m) not single spikes.
  - Add recovery threshold to avoid flap loops.
- Anomaly-based alerts:
  - Use only for high-volume stable baselines.
  - Always pair with hard guardrails for catastrophic failures.
- Observability-to-alert mapping:
  - `crash_rate > threshold` -> stability incident.
  - `api_failure_rate` spike -> availability/dependency incident.
  - `offline_queue_replay_failures` burst -> sync reliability incident.
  - `authentication_error_rate` burst -> auth incident.
  - `latency_p95/p99` extreme -> performance incident.
- Incident timeline capture:
  - Record `detected_at`, `ack_at`, `mitigation_start`, `stabilized_at`, `resolved_at`, and rollback actions.
- Alert deduplication:
  - Group by `signal_class + env + platform + release + feature_area`.
  - Suppress duplicate pages during active incident state.
- Runbook linkage:
  - Every paging alert must include a runbook URL and first 3 diagnostic checks.
  - Missing runbook blocks promotion of alert to paging severity.

## Anti-Patterns
- Alert storms from noisy or unbounded signals.
- Paging directly from raw high-cardinality event streams.
- Mixing dev/staging alerts with production escalation channels.
- Emitting alerts without explicit severity classification.
- Triggering pages without linked runbooks.
- Ignoring retry storms or duplicate burst patterns as noise.
- Alerting on unsampled low-volume metrics without minimum sample gates.
- Leaving stale alerts active after incident resolution.

## Decision Tree
```text
If environment == production
  -> paging alerts enabled with SEV mapping and escalation policy.
Else
  -> route to non-paging channels; validate thresholds only.

If signal_volume == high
  -> allow anomaly + rate-based rules with dedupe/cooldown.
Else
  -> use threshold + sustained-window rules only.

If error pattern == sudden spike
  -> classify as burst incident; require rapid triage and rollout correlation checks.
Else if error pattern == sustained degradation
  -> classify as degradation incident; evaluate rollback vs mitigation.

If signal_type == crash/ANR
  -> prioritize stability severity path.
Else if signal_type == latency/performance
  -> prioritize performance severity path with user-impact gates.

If mitigation can be automated safely
  -> execute predefined automation (feature flag rollback, traffic guard).
Else
  -> manual on-call response with incident commander when SEV1/SEV2.
```

## Execution Workflow
1. Collect required inputs and document assumptions.
2. Classify telemetry signals into alertable operational events.
3. Define SEV1-SEV4 taxonomy with user-impact criteria.
4. Define alert thresholds, sustained/rate windows, and sample-size guards.
5. Map each alert to ownership and escalation path.
6. Attach runbooks and required diagnostics per alert.
7. Define incident lifecycle states and state transitions.
8. Define resolution criteria and recovery validation checks.
9. Verify alert noise levels with historical replay/simulation.
10. Produce output using the Output Contract exactly.

## Edge Cases
- Crash spike immediately after release rollout.
- Telemetry ingestion outage hides real failures.
- Retry storms create artificial error spikes.
- Backend outage manifests as mobile incident.
- Stale alerts continue firing after mitigation/resolution.
- Alert dedupe failure pages multiple teams for same event.
- Mobile network instability causes false-positive error bursts.
- Region-specific outage impacts subset of users only.
- Symbolication delay obscures crash root cause.
- Low-traffic periods trigger noisy percentage-based alerts.

## Observability
Operational alert metrics/signals must be bounded, privacy-safe, and actionable:
- `crash_rate`
- `anr_rate` / freeze rate
- `api_failure_rate`
- `auth_error_rate`
- `latency_p95`, `latency_p99`
- `offline_queue_replay_failure_rate`
- `offline_queue_depth`
- `alert_trigger_rate`
- `alert_ack_time`
- `incident_mttr`

Rules:
- Include `env`, `platform`, `release`, and `feature_area` labels.
- Enforce cardinality caps; no user-entered text in labels.
- Use telemetry freshness checks; stale pipelines should suppress or downgrade alert severity.

## Output Contract
Return sections in this exact order:

1. Context Summary
2. Assumptions
3. Alertable Signals
4. Severity Taxonomy
5. Alert Threshold Design
6. Escalation Policy
7. Incident Lifecycle
8. Runbook Links
9. Noise Mitigation Strategy
10. Verification Checklist
11. Risks / Rollback
12. Next Implementation Step

## Verification Checklist
- Alert signals are mapped from telemetry with clear ownership.
- SEV1-SEV4 definitions are explicit and user-impact-based.
- Threshold/rate windows include minimum sample-size guards.
- Production and non-production routing is strictly separated.
- Dedupe keys and cooldown windows prevent alert storms.
- Every paging alert includes runbook and diagnostics checklist.
- Escalation policy defines ack timeout and re-page behavior.
- Incident lifecycle states and resolution criteria are explicit.
- Telemetry freshness/ingestion delay handling prevents false paging.
- Alert set passes noise-budget validation against historical data.

## Risks / Rollback
- Risk: noisy rules overload on-call.
  - Rollback: raise thresholds, increase sustained windows, and demote non-critical alerts to ticket channel.
- Risk: missed incidents from over-aggressive suppression.
  - Rollback: tighten suppression scope and add secondary guardrail alerts.
- Risk: ingestion lag causes false negatives/positives.
  - Rollback: add pipeline-health alerts and fallback backend-derived monitors.
- Risk: rollout-specific regression causes cascading incidents.
  - Rollback: trigger release rollback/feature kill switch and isolate affected cohort.

## Example Request
```text
Telemetry backend: Datadog
Alerting platform: PagerDuty
Environment scope: staging + prod
Ownership: Mobile Platform team + Feature teams
On-call: primary/secondary 24x7
Severity levels: SEV1-SEV4
Escalation: ack 5m, re-page 10m, SEV1 manager escalation 15m
Noise tolerance: max 6 pages/day/team
Platform coverage: iOS + Android
Signals: crash_rate, anr_rate, api_failure_rate, auth_error_rate, latency_p95, replay_failures
```

## Example Response Shape
```text
Context Summary
- ...

Assumptions
- ...

Alertable Signals
- ...

Severity Taxonomy
- ...

Alert Threshold Design
- ...

Escalation Policy
- ...

Incident Lifecycle
- ...

Runbook Links
- ...

Noise Mitigation Strategy
- ...

Verification Checklist
- ...

Risks / Rollback
- ...

Next Implementation Step
- ...
```
