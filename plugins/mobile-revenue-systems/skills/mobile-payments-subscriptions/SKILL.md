---
name: mobile-payments-subscriptions
description: Execution-grade skill for designing mobile payment and subscription systems with deterministic purchase handling, receipt validation, entitlement resolution, and platform-aware lifecycle management.
metadata:
  domain: mobile-revenue-systems
---

# Skill: mobile-payments-subscriptions

## Purpose

Design deterministic, platform-aware mobile purchase and subscription systems with validated transactions, idempotent entitlement handling, and rollback-safe recovery.

## When to Use

- Implementing one-time purchases or subscriptions in iOS/Android apps.
- Designing receipt validation and entitlement authority boundaries.
- Hardening restore purchases and multi-device entitlement sync.
- Handling renewal, grace period, billing retry, revocation, and refunds.
- Defining observability and failure handling for monetization-critical flows.

## When Not to Use

- UI-only paywall design with no transaction or entitlement logic changes.
- Backend-only billing tasks with no mobile purchase flow impact.
- Ad monetization workflows unrelated to App Store / Play billing.

## Required Inputs

- `target_platform`: `ios` | `android` | `both`
- product types: `consumable` | `non-consumable` | `subscription`
- subscription model: `auto-renewing` | `non-renewing`
- pricing tiers and currency model
- backend validation availability
- receipt validation strategy: `client` | `server` | `hybrid`
- entitlement model and feature-access mapping
- restore purchases requirements
- multi-device sync requirements
- offline purchase handling requirements
- fraud risk tolerance
- trial and grace-period usage
- billing retry behavior expectations
- store configuration status: App Store Connect / Play Console
- release environment: `sandbox` | `production`
- legal/compliance requirements (tax, disclosures, terms)

## Framework-Specific Directives

- Treat App Store and Play Store transaction state as store-authoritative input.
- Never trust client-side purchase state without validation.
- Treat subscription lifecycle as asynchronous and event-driven.
- Separate purchase initiation from entitlement resolution.
- Keep restore purchases deterministic and idempotent.
- Never block UI indefinitely on network validation.
- Treat pricing/currency as store-controlled; do not compute price in-app.
- Handle iOS and Android divergences explicitly.
- Prefer server-side receipt validation when available.

## Provider-Specific Directives

- App Store / StoreKit:
  - Treat pending/renewal/revocation updates as asynchronous state changes.
  - Validate receipt status before granting or extending entitlement.
- Google Play / Play Billing:
  - Enforce token/transaction acknowledgment and entitlement linkage rules.
  - Treat retry/grace/account-hold style lifecycle states explicitly.
- Hybrid validation backends:
  - Normalize iOS/Android transaction payloads into one entitlement contract.
  - Preserve original transaction identifiers for traceability.

## Technical Implementation Patterns

- Purchase lifecycle model:
  - `initiated -> pending -> completed -> validated -> entitlement_granted -> finished`
  - Define owner and state transition guard per step.
- Subscription lifecycle model:
  - `trial -> active -> grace -> expired -> canceled -> revoked`
  - Enforce lifecycle transitions from validated store state.
- Advanced subscription states:
  - Include `billing_retry`, `account_hold` (Android), `paused` (Android), `price_change_pending`.
  - Define transition guards for each advanced state.
- Upgrade / downgrade handling:
  - Handle plan changes mid-cycle with explicit effective-date rules.
  - Define entitlement transition timing for immediate vs deferred plan changes.
- Effective entitlement timing:
  - Define exact start/stop rules relative to validation and store lifecycle events.
  - Prevent entitlement activation on unvalidated transitional states.
- Entitlement resolution:
  - Derive access from validated receipt/subscription state.
  - Never infer entitlement from UI flags or local optimistic state.
- Entitlement authority boundaries:
  - Backend is source of truth for entitlement state.
  - Store is source of transaction validity.
  - Client is rendering layer only.
  - Define conflict resolution rules when backend/store/client states diverge.
- Receipt validation model:
  - Prefer backend validation.
  - Define fallback/degraded behavior when validator unavailable.
- Idempotent purchase handling:
  - Process each transaction identity once.
  - Ignore duplicate callbacks/events with same transaction ID.
- Restore purchases model:
  - Reconcile historical transactions deterministically.
  - Merge restored records into current entitlement state using authority rules.
- Backend authority model:
  - Backend computes final entitlement state.
  - Client renders entitlement state and pending transitions.
- Multi-device entitlement consistency:
  - Ensure all devices converge to backend entitlement state.
  - Invalidate stale local entitlement cache on authoritative updates.
  - Trigger reconciliation on app start, restore, and periodic sync.
- Retry and reconciliation:
  - Retry validation with bounded policy on transient failures.
  - Run periodic reconciliation against backend/store state.
- Drift handling:
  - Detect mismatch between store-reported state and backend entitlement state.
  - Trigger reconciliation flow when drift detected.
  - Prevent permanent divergence with bounded retry and manual-review fallback.
- Platform divergence:
  - Maintain explicit iOS vs Android handling branches.
  - Keep shared entitlement contract platform-agnostic.
- Transaction identity:
  - Use transaction ID/purchase token as dedupe and audit key.
  - Persist mapping between store transaction and entitlement record.
- Replay safety:
  - Retried validation or restore must not duplicate entitlement grants.
  - Suppress repeated terminal processing for same transaction identity.
- Idempotent entitlement resolution:
  - Derive entitlement state from canonical validated records.
  - Avoid incremental mutation that can double-grant access.
- Pricing and currency handling:
  - Always use localized store-provided pricing.
  - Prevent hardcoded currency symbols or conversion assumptions.

## Anti-Patterns

- Trusting client-only purchase state.
- Granting entitlement before validation completes.
- Ignoring duplicate callbacks/webhooks.
- Mixing UI state with billing authority state.
- Skipping restore purchases flow.
- Assuming immediate purchase success/finality.
- Ignoring subscription lifecycle transitions.
- Hardcoding pricing or currency logic.
- Ignoring pending/failed transaction states.
- Ignoring billing retry and grace periods.

## Decision Tree

- If product type is `consumable`:
  - Validate purchase and grant one-time consumable entitlement.
- If product type is `subscription`:
  - Resolve entitlement by subscription lifecycle state.
- If purchase is validated:
  - Proceed to entitlement resolution.
- If purchase is unvalidated:
  - Keep pending state; do not grant entitlement.
- If validation strategy is server/hybrid and backend available:
  - Use backend-authoritative entitlement result.
- If restore requested:
  - Reconcile historical transactions before new purchase flow decisions.
- If subscription state is `active` or `grace`:
  - Maintain entitlement.
- If subscription state is `expired`, `canceled`, or `revoked`:
  - Remove or downgrade entitlement by policy.
- If network/validator unavailable:
  - Buffer and retry validation; keep deterministic pending UX.
- If platform is iOS vs Android:
  - Execute platform-specific purchase/ack/state handling branch.

## Execution Workflow

1. Collect inputs.
2. Classify product types and platform constraints.
3. Define purchase and subscription lifecycle.
4. Define receipt validation strategy.
5. Define entitlement resolution logic.
6. Define idempotency and deduplication rules.
7. Define restore and reconciliation strategy.
8. Define failure and retry handling.
9. Define observability signals.
10. Validate deterministic, idempotent, and cross-device-consistent behavior across flows.
11. Produce structured output.

## Edge Cases

- Duplicate purchase callbacks for the same transaction.
- Purchase completes but validation fails.
- User changes device and runs restore purchases.
- Subscription renews while app is offline.
- Grace period starts after billing failure.
- Billing retry exhausts without recovery.
- Purchase is revoked or refunded after prior entitlement.
- Sandbox and production receipt/environment mismatch.
- Delayed store notifications arrive after local state changes.
- Entitlement mismatch between client and backend.
- Currency/pricing mismatch in displayed vs store-confirmed value.
- Purchase flow interrupted mid-transaction.
- Subscription upgrade/downgrade occurs mid-cycle.
- Grace period ends while device is offline.
- Restore returns stale local state before backend reconciliation.
- Multiple devices hold inconsistent entitlement cache.
- Backend temporarily out of sync with store lifecycle state.
- Duplicate restore triggered concurrently across devices.

## Observability

- Track purchase success/failure rate.
- Track validation success/failure rate.
- Track duplicate transaction rate.
- Track entitlement mismatch rate.
- Track subscription churn and expiration rate.
- Track renewal success/failure rate.
- Track restore success/failure rate.
- Track purchase-to-entitlement latency.
- Track restore-to-entitlement latency.
- Track billing retry outcomes.
- Track validation retry exhaustion rate.
- Track store vs backend entitlement drift rate.
- Track multi-device entitlement inconsistency rate.
- Track fraud/anomaly indicators (if enabled).
- Never log raw receipt payloads, payment identifiers, or sensitive user data.

## Output Contract

- Context Summary
- Assumptions
- Purchase Lifecycle
- Subscription Lifecycle
- Validation Strategy
- Entitlement Model
- Transaction Identity Rules
- Restore/Reconciliation Plan
- Failure Modes
- Observability Signals
- Risks / Rollback
- Next Implementation Step

## Verification Checklist

- Purchases are validated before entitlement grant.
- Duplicate transactions are handled idempotently.
- Restore flow is deterministic.
- Subscription lifecycle states are fully handled.
- Entitlement derives from validated authority state.
- Platform differences are explicitly handled.
- Failure and retry scenarios are covered.
- Observability covers all critical monetization flows.

## Risks / Rollback

- Risk: incorrect entitlement granting.
  - Rollback: revoke entitlement using authoritative state and re-run reconciliation.
- Risk: validation service failures.
  - Rollback: switch to degraded pending state with bounded retry and manual review path.
- Risk: duplicate transaction processing.
  - Rollback: enforce dedupe key repair and replay-safe reconciliation.
- Risk: subscription misclassification.
  - Rollback: recompute lifecycle state from canonical store/backend records.
- Risk: pricing/currency mismatch.
  - Rollback: force store-sourced display refresh and disable stale price cache.
- Risk: fraud or abuse patterns.
  - Rollback: tighten validation rules, flag suspicious transactions, delay entitlement grant.
- Risk: store misconfiguration.
  - Rollback: halt affected SKU release and revert to known-good configuration.

## Example

- Scenario:
  - User subscribes to premium plan.
  - Flow: purchase initiated -> completed -> server validation -> entitlement granted.
- Renewal:
  - Renewal event validated; entitlement remains active.
- Failure:
  - Validation timeout keeps entitlement in pending state; retry policy runs; no duplicate grant.
- Restore:
  - User installs on new device, runs restore, server reconciliation restores active entitlement.

## Example Request

"Use this skill to produce a production-safe implementation plan for this app scenario, including assumptions, architecture choices, validation steps, and rollback notes."

## Example Response Shape

- Context Summary
- Assumptions
- Implementation Plan
- Validation Checklist
- Risks / Rollback
- Next Implementation Step
