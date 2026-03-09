---
name: mobile-realtime-websocket
description: Execution-grade skill for designing resilient realtime websocket architectures in React Native and Expo apps.
metadata:
  domain: mobile-data-platform
---

# Skill: mobile-realtime-websocket

## Purpose
Design deterministic realtime communication layers for React Native and Expo apps that remain correct under mobile network volatility, app lifecycle transitions, reconnect bursts, and authentication changes.

## When to Use
- Building websocket/subscription-based live updates for feeds, chats, presence, or collaboration data.
- Defining reconnect, deduplication, ordering, and replay behavior after disconnects.
- Integrating server push events into query/cache state with deterministic invalidation.
- Hardening mobile realtime behavior for offline fallback and background/foreground transitions.
- Designing observability signals for realtime reliability and incident response.

## When Not to Use
- Polling-only systems with no persistent or pseudo-persistent realtime channel.
- Backend-only pub/sub architecture without mobile client responsibilities.
- UI-only local state updates unrelated to network-delivered events.

## Required Inputs
Collect all inputs first; missing values must be explicit assumptions.

- Realtime backend type: `WebSocket` | `Socket.IO` | `GraphQL subscriptions` | custom.
- Authentication model: bearer token, signed socket token, cookie session, token refresh strategy.
- Event schema: event names, payload versions, required metadata, schema evolution rules.
- Message ordering guarantees: global order, per-channel order, per-entity order, or none.
- Retry/reconnect policy: max attempts, backoff+jitter, cooldown, reconnect gates.
- Message idempotency strategy: event IDs, sequence numbers, dedupe window.
- Offline behavior: queue outbound messages, drop policy, polling fallback behavior.
- Event persistence requirements: durable event cursor/offset storage, replay lookback window.
- Message rate constraints: expected throughput, burst limits, client backpressure policy.
- Server fan-out model: topic/channel, room, user-targeted, broadcast.
- Cache/query stack: `@tanstack/react-query` | `RTK Query` | custom cache.
- Lifecycle constraints: foreground/background handling, app suspension, platform limits.

## Framework-Specific Directives
### Expo Managed
- Use native `WebSocket` API or `socket.io-client` only when backend protocol requires it.
- Use `@react-native-community/netinfo` to gate connect/reconnect attempts.
- Treat background execution as best-effort; reconnect on foreground resume with replay cursor sync.
- Integrate events with `@tanstack/react-query` or equivalent via targeted cache updates/invalidation.

### Expo + EAS
- Follow Expo Managed directives plus environment-safe endpoint/auth config per EAS profile.
- Include release channel/build identifiers in websocket connection metadata for diagnostics.
- Use remote config/feature flags to tune reconnect/backpressure policy without app rebuild.
- Ensure staging/prod realtime endpoints and credentials are fully separated.

### Bare React Native
- Use native `WebSocket` API, `socket.io-client`, or GraphQL subscription clients (`graphql-ws`, Apollo subscriptions) per backend contract.
- Add AppState-aware connection control for suspend/resume transitions.
- Prefer durable storage (SQLite/Realm/MMKV as appropriate) for replay cursors and unsent outbound queue metadata.
- Ensure single connection coordinator to avoid duplicate sockets per feature.

## Provider-Specific Directives
### Raw WebSocket
- Define explicit handshake contract (auth, protocol version, subscription topics).
- Implement application-level ACK/sequence handling when protocol does not provide ordering guarantees.

### Socket.IO
- Use namespace/room semantics for scoped fan-out and controlled subscription surfaces.
- Validate reconnect behavior with transport upgrades and fallback constraints.

### GraphQL Subscriptions
- Keep schema versioning aligned between query and subscription payloads.
- Reconcile subscription events with query cache by entity identity; do not blindly refetch entire domains.

### Custom Realtime Gateway
- Require stable event IDs, server timestamp/sequence, and replay endpoint for recovery.
- Enforce per-topic rate limits and backpressure metadata in protocol.

## Technical Implementation Patterns
- Connection lifecycle state machine:
  - States: `idle -> connecting -> authenticating -> subscribed -> degraded -> reconnect_wait -> closed`.
  - Only one authoritative state machine instance per session/user context.
- Reconnect strategy:
  - Exponential backoff with jitter and max reconnect ceiling.
  - Pause reconnect while offline, on auth invalid state, or when app is backgrounded (unless explicitly required).
- Heartbeat and liveness:
  - Track ping/pong or heartbeat event freshness.
  - Transition to degraded/reconnect when heartbeat timeout exceeds threshold.
- Message queue buffering:
  - Buffer outbound messages during transient disconnect with bounded queue size.
  - Apply explicit drop/retry policy when queue exceeds capacity.
- Ordering rules:
  - Enforce sequence checks (global/per-channel/per-entity based on contract).
  - Detect gaps and trigger replay sync before applying newer events.
- Event deduplication:
  - Maintain sliding dedupe window keyed by `event_id` or `(entity_id, sequence)`.
  - Skip duplicates before cache mutation side effects.
- Replay synchronization:
  - Persist last applied cursor/sequence durably.
  - On reconnect, replay from cursor and resolve gaps before resuming live stream.
- Cache/query integration:
  - Map event types to targeted cache patch or invalidation rules.
  - Keep websocket transport state separate from UI view state.
- Backpressure control:
  - Bound per-tick event processing and defer overflow batches.
  - Prefer coalescing high-frequency updates into summarized cache patches.
- Connection health monitoring:
  - Emit health snapshots (state, reconnect attempts, heartbeat latency, queue depth).
  - Escalate to polling fallback when health remains degraded beyond threshold.

## Anti-Patterns
- Uncontrolled reconnect loops without backoff or connectivity gates.
- Processing duplicate events without dedupe IDs/windows.
- Ignoring ordering/sequence constraints and applying out-of-order events blindly.
- Mixing websocket transport state directly into component UI state.
- Missing heartbeat/liveness detection and stale-connection recovery.
- Unbounded event processing causing UI jank or memory pressure.
- Replaying full dataset on every reconnect without cursor strategy.
- Keeping dev/staging/prod websocket streams in shared routing/credentials.

## Decision Tree
```text
If backend supports stable websocket/subscription channel and mobile reliability is acceptable
  -> use realtime primary path.
Else
  -> use polling primary path with optional websocket enhancement.

If updates are high-frequency and user-visible latency is critical
  -> event-driven cache patching with tight dedupe/order controls.
Else
  -> request-driven refresh with selective event invalidation.

If priority == low latency
  -> keep persistent connection while foreground; lighter buffering, faster reconnect.
Else if priority == reliability
  -> stronger replay cursor guarantees, stricter ordering checks, conservative apply rules.

If app lifecycle/network conditions prevent stable persistent socket
  -> use ephemeral connection model + polling fallback.
Else
  -> persistent connection with health monitoring and controlled reconnect.
```

## Execution Workflow
1. Collect required inputs and record assumptions.
2. Define connection lifecycle state machine and ownership boundaries.
3. Define authentication handshake and token refresh coupling.
4. Define message/event schema, versioning, and sequencing fields.
5. Implement reconnect strategy (backoff, jitter, gates, cooldown).
6. Integrate realtime events with cache/query update and invalidation rules.
7. Implement deduplication and ordering enforcement with replay cursor support.
8. Implement heartbeat and connection health monitoring.
9. Validate offline behavior, fallback path, and reconnect correctness.
10. Produce output using the Output Contract exactly.

## Edge Cases
- Connection drops during high-volume message burst.
- Duplicate events delivered after reconnect or server retry.
- Out-of-order event arrival with missing sequence gaps.
- Server restart resets subscriptions and loses transient state.
- Auth token expires during active connection/authenticated subscription.
- Network switches (`wifi -> cellular`) causing transient socket invalidation.
- App background suspension tears down connection unexpectedly.
- Connectivity restore triggers reconnect flood across many clients.
- Replay cursor corrupted or stale after app restart.
- Event burst overwhelms client processing and blocks UI thread.

## Observability
Track bounded, actionable realtime reliability signals:
- `websocket_connection_state`
- `websocket_reconnect_rate`
- `websocket_message_rate`
- `websocket_error_rate`
- `websocket_latency`
- `duplicate_event_rate`
- `event_gap_detected_rate`
- `outbound_queue_depth`

Rules:
- Include dimensions: `environment`, `platform`, `channel/topic`, `release`.
- Never log sensitive payload content or auth tokens.
- Emit counters for fallback activation and replay sync duration.

## Output Contract
Return sections in this exact order:

1. Context Summary
2. Assumptions
3. Realtime Architecture
4. Connection Lifecycle
5. Reconnect Strategy
6. Message Ordering Rules
7. Event Deduplication Strategy
8. Cache Integration
9. Failure Handling
10. Verification Checklist
11. Risks / Rollback
12. Next Implementation Step

## Verification Checklist
- Lifecycle state machine and transitions are explicit and deterministic.
- Auth handshake and refresh behavior are defined for connect/reconnect.
- Reconnect policy is bounded (backoff, jitter, max attempts, gating).
- Ordering logic handles sequence gaps and out-of-order events safely.
- Deduplication window prevents duplicate event side effects.
- Replay cursor persistence and reconnect sync path are implemented.
- Cache integration rules are targeted (patch/invalidate) and non-destructive.
- Heartbeat/health monitoring detects stale connections promptly.
- Offline fallback path and reconnect flood protections are validated.
- Observability signals are emitted with privacy-safe metadata.

## Risks / Rollback
- Risk: reconnect storms overload backend after outage.
  - Rollback: increase reconnect jitter/cooldown and temporarily enforce polling fallback.
- Risk: ordering/dedupe bug corrupts client state.
  - Rollback: disable direct patching for affected events and switch to invalidate+refetch mode.
- Risk: auth coupling breaks socket re-auth after token rotation.
  - Rollback: force connection reset on token refresh and gate subscriptions until valid auth.
- Risk: event flood degrades app responsiveness.
  - Rollback: apply stricter backpressure caps and coalescing thresholds via feature flags.

## Example Request
```text
Realtime backend: WebSocket
Authentication model: short-lived JWT with refresh token
Event schema: versioned events with event_id + sequence
Ordering guarantees: per-channel ordering only
Reconnect policy: exponential backoff with jitter, max 8 attempts
Idempotency strategy: dedupe by event_id with 5-minute sliding window
Offline behavior: outbound queue buffered, polling fallback for critical views
Event persistence: persist replay cursor per channel
Message rate constraints: 200 msg/min burst
Server fan-out: user channels + room channels
Cache/query layer: TanStack Query
```

## Example Response Shape
```text
Context Summary
- ...

Assumptions
- ...

Realtime Architecture
- ...

Connection Lifecycle
- ...

Reconnect Strategy
- ...

Message Ordering Rules
- ...

Event Deduplication Strategy
- ...

Cache Integration
- ...

Failure Handling
- ...

Verification Checklist
- ...

Risks / Rollback
- ...

Next Implementation Step
- ...
```
