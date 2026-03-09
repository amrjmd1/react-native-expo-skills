# Mobile Data Platform Plugin

Data-system pack for resilient API, offline, and realtime behavior.

## Scope

Use this pack for API query architecture, retry policy, offline sync and conflict resolution, and websocket lifecycle stability.

## Skills

1. `mobile-api-query-architecture`
Typed API boundaries, query strategy, cache ownership, retry/backoff policy.

2. `mobile-offline-sync-conflicts`
Offline queue semantics, idempotency, conflict detection/resolution.

3. `mobile-realtime-websocket`
Realtime lifecycle, reconnect strategy, ordering, deduplication, backpressure.

## Typical Senior Use Cases

- Separate server-state ownership from UI/view state.
- Eliminate sync inconsistency under intermittent connectivity.
- Stabilize realtime channels across foreground/background transitions.

## Quality Bar

- Deterministic data contracts.
- Explicit consistency model.
- Operationally visible failure handling.
