---
name: mobile-alerting-incident-response
description: Execution-grade skill for designing mobile alerting and incident response systems with deterministic signal thresholds, severity classification, escalation paths, and rollback-ready mitigation strategies.
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
- Target platform: `ios` | `android` | `both`.
- Telemetry signal types available: `events` | `logs` | `metrics`.
- Environment scope: `dev`, `staging`, `prod` with routing boundaries.
- Service ownership model: team ownership by feature/domain/platform.
- On-call rotation model: primary/secondary, timezone model, handoff rules.
- Escalation channels: pager, Slack, email, incident bridge.
- Severity taxonomy: `SEV1`-`SEV4` with user-impact definitions.
- SLO/SLA expectations and error-budget policy.
- Escalation policy: ack timeout, re-page policy, manager escalation, incident commander criteria.
- Alert noise tolerance: expected daily alert budget and false-positive budget.
- Mobile platform coverage: iOS, Android, app version cohort, release channel.
- Telemetry signals available: crash rate, ANR/freeze, API failure, auth error bursts, latency p95/p99, offline replay failures.
- Release cadence and release context: rollout strategy, canary cohorts, kill switch capability.
- User-impact thresholds and segmentation model (region/cohort/tier).
- Telemetry ingestion delay tolerance and fallback signal strategy.
- Rollback capability and mitigation constraints.
- Observability coverage level and known blind spots.

## Framework-Specific Directives
- Alerts must be derived from structured telemetry contracts, not ad-hoc signals.
- Alerting is a contract between telemetry quality and response ownership.
- Never alert on raw metrics without threshold, window, and context.
- Alerts must map to user impact, not anomaly alone.
- Avoid coupling alert triggers directly to UI logic.
- Alerts must be actionable; informational-only signals route to non-paging channels.
- Account for offline users and delayed telemetry in incident detection.
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
  - Classify by severity level: `info`, `warning`, `critical`.
  - Classify impact as `user-impact` vs `system-impact`.
  - Map each class to escalation target and response SLA.
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
- Signal-to-alert mapping:
  - Define deterministic mapping from telemetry signal to alert type.
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
- Incident correlation:
  - Correlate multi-signal failures into a single incident key.
  - Prevent fragmented incidents from same release/platform failure.
- Escalation model:
  - Escalate by severity and duration (time in incident state).
  - Enforce explicit owner at each escalation level.
- Alert fatigue control:
  - Suppress low-signal noisy rules.
  - Enforce per-signal alert budgets and auto-review for noisy rules.
- Rollback trigger model:
  - Define rollback/mitigation trigger thresholds by severity and blast radius.
  - Gate rollback on readiness checks and operator confirmation policy.
- Incident lifecycle:
  - `detect -> alert -> acknowledge -> mitigate -> resolve -> postmortem`.
- Alert determinism:
  - Identical signal conditions must produce identical alert behavior.
  - Remove timing-only branching from escalation logic.
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

If signal fails threshold/window/sample-size guards
  -> ignore for paging and keep as log-only signal.
Else
  -> evaluate severity and incident correlation.

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

If correlated signals share incident key
  -> open/update one incident.
Else
  -> create separate incident per deterministic key.

If mitigation can be automated safely
  -> execute predefined automation (feature flag rollback, traffic guard).
Else
  -> manual on-call response with incident commander when SEV1/SEV2.

If rollback trigger threshold met and rollback-ready artifact exists
  -> execute rollback.
Else
  -> wait/observe with bounded reassessment window.

If issue is platform-specific
  -> route to platform owner and platform-scoped incident.
Else
  -> route as global incident with cross-platform owner.
```

## Execution Workflow
1. Collect inputs.
2. Define critical flows and impact areas.
3. Map telemetry signals to alert conditions.
4. Define severity levels and thresholds.
5. Define deduplication and correlation rules.
6. Define escalation and ownership.
7. Define rollback/mitigation strategies.
8. Define observability signals for alert health.
9. Validate alert determinism and noise control.
10. Produce structured output.

## Edge Cases
- Crash spike immediately after release rollout.
- Alert triggered by partial telemetry coverage.
- Telemetry ingestion outage hides real failures.
- Delayed telemetry causes late alert.
- Retry storms create artificial error spikes.
- Duplicate alerts flood system during active incident.
- Backend outage manifests as mobile incident.
- Stale alerts continue firing after mitigation/resolution.
- Alert dedupe failure pages multiple teams for same event.
- Mobile network instability causes false-positive error bursts.
- Region-specific outage impacts subset of users only.
- Symbolication delay obscures crash root cause.
- Low-traffic periods trigger noisy percentage-based alerts.
- Alert triggered but no owner acknowledges in escalation window.
- Rollback trigger fires but rollback execution fails.
- Platform-specific failure is misclassified as global incident.
- Alert suppressed incorrectly during active user-impacting degradation.
- Telemetry missing on critical path causes silent detection failure.
- Correlated signals are not grouped, causing fragmented incident storms.
- User impact is underestimated and severity is set too low.

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
- `alert_noise_ratio`
- `alert_to_incident_conversion_rate`
- `alert_duplication_rate`
- `alert_ack_time`
- `mtta`
- `incident_mttr`
- `mttr`
- `false_positive_rate`
- `missed_incident_rate`
- `escalation_success_rate`
- `rollback_trigger_success_rate`
- `user_impact_correlation_rate`

Rules:
- Include `env`, `platform`, `release`, and `feature_area` labels.
- Enforce cardinality caps; no user-entered text in labels.
- Use telemetry freshness checks; stale pipelines should suppress or downgrade alert severity.

## Output Contract
Return sections in this exact order:

1. Context Summary
2. Assumptions
3. Alert Classification Model
4. Signal-to-Alert Mapping
5. Threshold Definitions
6. Deduplication / Correlation Rules
7. Escalation / Ownership Model
8. Rollback / Mitigation Plan
9. Observability Signals
10. Failure Modes
11. Risks / Rollback
12. Next Implementation Step

## Verification Checklist
- Alerts are actionable and owner-assigned.
- Severity levels are explicit and user-impact-based.
- Duplicate alerts for same incident are suppressed.
- Alert triggers are deterministic for identical signal conditions.
- Escalation path and ack policy exist.
- Rollback or mitigation path exists for critical incidents.
- Alert noise is bounded by budget and suppression rules.
- Alert quality observability metrics are defined.
- Alerts map to measurable user impact.

## Risks / Rollback
- Risk: noisy rules overload on-call.
  - Rollback: raise thresholds, increase sustained windows, and demote non-critical alerts to ticket channel.
- Risk: missed incidents from over-aggressive suppression.
  - Rollback: tighten suppression scope and add secondary guardrail alerts.
- Risk: ingestion lag causes false negatives/positives.
  - Rollback: add pipeline-health alerts and fallback backend-derived monitors.
- Risk: rollout-specific regression causes cascading incidents.
  - Rollback: trigger release rollback/feature kill switch and isolate affected cohort.
- Risk: incorrect severity classification delays response.
  - Rollback: reclassify severity rules and enforce stricter user-impact gates.
- Risk: alerting configuration drifts from telemetry contract.
  - Rollback: pin rule/schema versions and revalidate mapping before re-enable.

## Example
- Scenario:
  - Crash spike after release on Android production cohort.
  - `crash_rate` and `anr_rate` cross critical sustained thresholds.
- Response:
  - Signals correlate into one stability incident.
  - Pager escalation triggers primary then secondary on-call.
  - Rollback trigger model executes release rollback to previous stable build.
- Recovery:
  - Crash and ANR rates return below recovery threshold.
  - Incident resolved and postmortem opened with timeline and failure mode summary.
