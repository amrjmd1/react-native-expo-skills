---
name: mobile-background-notifications
description: Execution-grade skill for designing reliable, platform-aware mobile notification systems with deterministic routing, delivery constraints, background execution handling, and observability.
metadata:
  domain: mobile-user-experience-quality
---

# Skill: mobile-background-notifications

## Purpose

Design a reliable notification delivery and handling system for React Native and Expo apps with deterministic routing, idempotent behavior, and platform-aware background constraints.

## When to Use

- Implementing push notifications with deep-link or screen navigation targets.
- Hardening foreground/background/terminated notification behavior.
- Defining payload contracts, deduplication, and idempotent actions.
- Building permission UX and opt-in/opt-out lifecycle handling.
- Adding observability for delivery, interaction, and failure paths.

## When Not to Use

- In-app messaging features with no OS notification dependency.
- Backend-only fan-out logic without mobile handling changes.
- UI-only copy/style changes to notification templates.

## Required Inputs

- `target_platform`: `ios` | `android` | `both`
- app states: `foreground` | `background` | `terminated`
- notification types: `transactional` | `realtime` | `scheduled` | `silent` | `mixed`
- delivery urgency: `low` | `normal` | `high`
- expected reliability level and acceptable failure tolerance
- navigation targets: deep links, screen routes, required params
- permission model and UX strategy
- backend notification provider and token lifecycle assumptions
- payload size limits and schema structure
- background execution needs and safe-operation boundaries
- deduplication requirements and idempotency key source
- analytics/telemetry requirements
- offline and late-delivery tolerance
- user segmentation/targeting rules

## Framework-Specific Directives

- Treat iOS and Android delivery models as different systems.
- Assume background execution is limited and non-deterministic.
- Never depend on background execution for critical business completion.
- Treat terminated-state behavior as first-class, not fallback.
- Separate notification reception from navigation side effects.
- Validate permission transitions explicitly: grant, deny, revoke.
- Treat notification tap handling as a strict navigation contract.
- Do not assume delivery guarantees, especially on Android OEM variants.

## Technical Implementation Patterns

- Notification lifecycle model:
  - `received -> displayed -> interacted -> resolved`
  - Track state transitions with deterministic handlers.
- Delivery reliability model:
  - Treat delivery as best-effort.
  - Define fallback behavior when message is late, dropped, or stale.
- Idempotent handling:
  - Enforce one-time action execution per notification identity.
  - Persist processed IDs for replay-safe behavior.
- Notification routing system:
  - Map `payload -> intent -> navigation/action`.
  - Reject payloads missing route-critical fields.
- Background execution guard:
  - Allow only short, safe, side-effect-limited tasks.
  - Defer heavy or critical flows to foreground-ready state.
- Payload contract:
  - Define minimal required keys, version, intent type, and idempotency key.
  - Keep business logic out of payload content.
- Deduplication strategy:
  - Use notification ID plus timestamp/hash windows.
  - Deduplicate at receipt and pre-action stages.
- Permission state machine:
  - `requested -> granted -> denied -> revoked`
  - Bind UX and fallback behavior to each state.
- Navigation safety:
  - Queue navigation intents until app/navigation container is ready.
  - Apply single-consumer routing for tapped notifications.
- Timing model:
  - Handle delayed, stale, and out-of-order notifications explicitly.
  - Apply TTL/relevance checks before action.
- Platform divergence handling:
  - Maintain explicit iOS and Android handling branches.
  - Keep shared contract at payload/intent layer only.
- Notification determinism:
  - Identical payload and app state must produce identical routing and side effects.
  - Avoid non-deterministic branching based on timing or partial initialization.

## Anti-Patterns

- Assuming guaranteed delivery.
- Triggering navigation directly from background handlers.
- Ignoring duplicate notifications.
- Overloading payloads with business logic.
- Ignoring permission-denied or revoked states.
- Assuming identical platform behavior.
- Depending on notification ordering for correctness.
- Running heavy logic in background handlers.
- Coupling notification actions to implicit UI state.

## Decision Tree

- If app state is `foreground`:
  - Route to in-app handling policy and optional local presentation.
- If app state is `background`:
  - Store intent, validate payload, execute safe background subset.
- If app state is `terminated`:
  - Rehydrate intent on launch; navigate only when app is ready.
- If message type is `silent`:
  - Apply background guard and skip direct UI navigation.
- If permission is `granted`:
  - Process delivery and interaction path.
- If permission is `denied` or `revoked`:
  - Skip push-dependent flow; show permission recovery UX.
- If duplicate detected:
  - Ignore action side effects; record dedupe event.
- If real-time urgency is high and payload stale:
  - Drop or downgrade action by TTL policy.
- If routing target is invalid:
  - Store failure signal; route to safe fallback screen.
- If platform branch differs:
  - Execute platform-specific handler path.

## Execution Workflow

1. Collect inputs.
2. Classify notification types and app states.
3. Define payload contract.
4. Define delivery and reliability assumptions.
5. Define routing and navigation model.
6. Define background execution constraints.
7. Define deduplication and idempotency rules.
8. Define permission UX and state handling.
9. Define observability signals.
10. Validate deterministic and idempotent behavior under all app states.
11. Produce structured output.

## Edge Cases

- Notification arrives before app initialization completes.
- Duplicate notifications trigger duplicate actions.
- Notification is tapped but navigation stack is not ready.
- Background handler executes partially or not at all.
- Permission is revoked after prior opt-in.
- Notification arrives late and is no longer relevant.
- Multiple notifications arrive out of order.
- Payload misses required routing fields.
- iOS behavior diverges from Android for same payload.
- Deep link fails due to current navigation state mismatch.
- Notification tap triggers navigation before app state hydration completes.

## Observability

- Track delivery rate vs open/interact rate.
- Track notification handling failure rate by state and platform.
- Track duplicate detection and dedupe suppression rate.
- Track notification-driven navigation success rate.
- Track delayed/stale notification handling outcomes.
- Track permission opt-in/opt-out trends.
- Track background handler execution success/failure.
- Track dropped/ignored notifications by reason.
- Track iOS vs Android delivery and interaction deltas.
- Never log raw payload content containing sensitive user data.

## Output Contract

- Context Summary
- Assumptions
- Notification Types & States
- Payload Contract
- Routing / Navigation Model
- Background Handling Rules
- Deduplication Strategy
- Permission Model
- Failure Modes
- Observability Signals
- Risks / Rollback
- Next Implementation Step

## Verification Checklist

- Notification handling is idempotent.
- Navigation behavior is deterministic.
- Foreground/background/terminated states are all handled.
- Payload contract is validated before action.
- Permission states are explicitly handled.
- Background execution path is safe and bounded.
- Duplicate notifications do not produce duplicate side effects.
- Observability covers all critical paths and failure modes.

## Risks / Rollback

- Risks:
  - Missed or dropped notifications.
  - Duplicate side effects from repeated delivery.
  - Broken navigation from invalid/stale intents.
  - Permission-state mismatch and UX dead ends.
  - Background execution failure under OS constraints.
  - Platform-specific regressions.
- Rollback rules:
  - Keep previous known-good notification routing config active until new path is verified.
  - Gate rollout by platform-specific failure and navigation-success thresholds.
  - Disable risky notification intents via server-side targeting rules if regression spikes.
  - Revert to safe fallback routing when deterministic handling cannot be guaranteed.

## Example

- Scenario:
  - Transactional push for order update routes user to `OrderDetails` screen.
  - Must work in foreground, background, and terminated states.
- Implementation:
  - Payload includes `notification_id`, `intent_type=order_update`, `order_id`, `ttl`, `route`.
  - Handler validates payload, deduplicates by `notification_id`, queues navigation intent until app-ready.
  - Background path stores intent only; heavy data sync deferred to foreground.
- Validation:
  - Verify deterministic route in all app states.
  - Verify duplicate push does not duplicate navigation or action.
  - Verify stale notification beyond `ttl` is ignored.
  - Verify permission-denied path shows recovery UX.
