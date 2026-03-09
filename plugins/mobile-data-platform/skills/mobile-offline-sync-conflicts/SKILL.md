---
name: mobile-offline-sync-conflicts
description: Execution-grade skill for offline mutation replay, conflict detection, and reconciliation strategy in React Native and Expo apps.
metadata:
  domain: mobile-data-platform
---

# Skill: mobile-offline-sync-conflicts

## Purpose
Design deterministic offline synchronization for React Native and Expo apps where local writes are queued, replayed after connectivity recovery, and reconciled safely under conflict, retry, and partial-failure conditions.

## When to Use
- Building or hardening offline write support with durable local queues.
- Defining conflict detection/resolution between local edits and remote state.
- Handling optimistic updates with rollback on replay rejection.
- Implementing replay workers, retry backoff, and reconnect bootstrapping.
- Designing authority boundaries for local-first, server-first, or hybrid sync models.

## When Not to Use
- Read-only offline caching with no local mutations.
- Backend-only conflict engines with no mobile queue/state responsibilities.
- UI-only tasks with no sync lifecycle, queue durability, or replay logic.

## Required Inputs
Collect these inputs before implementation. If missing, state assumptions explicitly.

- Platform mode: `Expo Managed` | `Expo + EAS` | `Bare React Native`.
- Local storage layer: `expo-sqlite` | `react-native-mmkv` | `AsyncStorage` | `Realm` | custom.
- Cache/query layer: `@tanstack/react-query` | `RTK Query` | custom cache | none.
- Backend sync model: `REST` | `GraphQL` | custom delta sync | websocket-assisted sync.
- Source of truth: `local-first` | `server-first` | `hybrid`.
- Entity types being synced and mutation types per entity (`create/update/delete/patch`).
- Conflict tolerance per entity: `low` | `medium` | `high`.
- Offline write support: `yes` | `no` | `limited` (which endpoints allowed).
- Optimistic update policy: immediate commit, staged optimistic, or disabled.
- Auth/session dependency during replay: token source, refresh behavior, failure mode.
- Retry constraints: max attempts, backoff policy, jitter, replay window, battery/network gates.
- Reconciliation ownership: `client` | `server` | `hybrid`.
- Idempotency strategy: mutation IDs, request keys, dedupe window on backend.
- Versioning signals: revision column, ETag/If-Match, updatedAt, server sequence.

## Framework-Specific Directives
### Expo Managed
- Use `expo-sqlite` for durable queue + sync metadata tables when writes must survive process kill/reboot.
- Use `@react-native-community/netinfo` for connectivity transitions; do not replay on every transient network flap.
- If using `@tanstack/react-query`, keep optimistic cache state separate from durable mutation queue records.
- Avoid large queue payloads in `AsyncStorage`; reserve it for small flags/config only.

### Expo + EAS
- Follow Expo Managed directives plus environment-specific replay controls (staging/prod endpoints, release channels).
- Gate replay during app startup until auth/session restoration finishes.
- Use EAS build profiles to keep sync feature flags deterministic across environments.
- If background execution is required, define platform limitations clearly; never assume continuous worker execution.

### Bare React Native
- Prefer SQLite (`react-native-sqlite-storage` or equivalent) or Realm for high-volume queues.
- Use `react-native-mmkv` for lightweight sync flags/cursors, not as sole store for durable ordered queue history.
- For Headless JS/background tasks, treat execution as best-effort; queue durability and resumability remain mandatory.
- Keep native module failures isolated from queue integrity (failed native call must not drop queued mutations).

## Provider-Specific Directives
### REST
- Send idempotency key per mutation (`Idempotency-Key` header or body field).
- Use revision checks (`If-Match`, version field) for updates/deletes.
- Treat HTTP `409/412` as conflict path, not generic retry.

### GraphQL
- Include mutation client ID and entity revision in input types.
- Distinguish transport errors from resolver business conflicts.
- Prefer explicit conflict payloads in schema over opaque error strings.

### Custom Delta Sync / Websocket-Assisted
- Track per-entity or per-collection watermark/cursor.
- Pause pull-apply while replay commit stage is running, or enforce deterministic ordering barrier.
- Websocket invalidation during replay should schedule post-replay reconciliation pull, not interrupt in-flight mutation commit.

## Technical Implementation Patterns
- Local mutation queue:
  - Durable record fields: `queue_id`, `entity_type`, `entity_id`, `op`, `payload_ref|payload`, `base_revision`, `mutation_id`, `created_at`, `attempt_count`, `status`, `last_error_code`.
  - Enforce deterministic order key (`created_at`, monotonic local sequence).
  - Queue compaction:
    - After successful replay ACK, compact/prune old queue records.
    - Preserve ordering guarantees and keep idempotency/dedupe records for the required dedupe window.
    - Use a sliding replay window, compacted history marker, or periodic compaction jobs.
- Sync worker / replay loop:
  - Single-writer lock to prevent parallel replay races.
  - Batch by entity domain only if backend contract supports ordering guarantees.
- Conflict detector:
  - Detect via server revision mismatch, conflict status code, or divergent tombstone/version state.
  - Classify conflict type (`update-update`, `delete-update`, `duplicate-replay`, `outdated-base`).
- Conflict artifact storage:
  - Persist unresolved conflicts durably when auto-merge is not possible.
  - Store `entity_type`, `entity_id`, `base_revision`, `server_revision`, `local_payload_reference`, `conflict_type`, `detected_at`.
  - Keep artifacts queryable for manual resolution, automated reconciliation jobs, and user-facing conflict UI.
- Merge policy layer:
  - Per-entity policy map (`LWW`, field-level merge, server-authoritative, manual resolution required).
  - Never resolve without explicit ownership rule.
- Tombstone/delete handling:
  - Persist delete intent as first-class queue op; do not collapse into cache eviction.
  - Preserve tombstone metadata until server ack + reconciliation complete.
- Retry scheduler:
  - Exponential backoff + jitter, capped attempts, retry class by error category.
  - Do not retry deterministic validation failures (`4xx` domain errors).
- Watermark/cursor tracking:
  - Maintain durable `last_successful_pull_cursor` separate from replay queue pointers.
  - Advance cursor only after apply success.
- Version/revision checks:
  - Attach `base_revision` (or equivalent) to update/delete mutations.
  - Backend must reject stale base revisions predictably.

## Anti-Patterns
- Replaying queued mutations without idempotency keys or dedupe checks.
- Treating stale local cache as resolved truth without revision validation.
- Blindly overwriting server state on reconnect.
- Dropping delete intent during offline windows.
- Mixing ephemeral optimistic UI state with durable queue state.
- Allowing out-of-order mutation replay for same entity without explicit commutativity guarantees.
- Running multiple replay workers concurrently against the same queue.
- Resolving conflicts without declared ownership boundaries.
- Logging raw sensitive payloads in sync telemetry.

## Decision Tree
```text
If source_of_truth == local-first
  -> local state is UX truth; queue writes immediately; require strong conflict detector on replay.
Else if source_of_truth == server-first
  -> local writes are provisional; limit optimistic depth; prioritize server reconciliation pulls.
Else (hybrid)
  -> define per-entity authority map before implementation.

If entity mutability == append-only
  -> prefer idempotent create semantics; low merge complexity.
Else (editable)
  -> require base_revision and explicit merge policy per entity.

If mutation is destructive (delete/hard archive)
  -> persist tombstone op; block conflicting local updates until delete resolves.
Else
  -> allow normal update/create replay path.

If reconciliation_ownership == client
  -> implement deterministic local merge engine + conflict state persistence.
Else if reconciliation_ownership == server
  -> send conflict context; accept canonical server result; rebase local projections.
Else (hybrid)
  -> server resolves structural conflicts; client resolves presentation fields only.

If conflict strategy == LWW
  -> use only for low-conflict, low-criticality entities with acceptable loss semantics.
Else if strategy == version-based merge
  -> require revisioned fields + deterministic merge rules.
Else (manual conflict handling)
  -> persist conflict artifacts and block final commit until user/system resolution.

If domain_conflict_rate == high
  -> avoid global LWW; use fine-grained merge + server arbitration + conflict observability alerts.
Else
  -> simpler policy allowed with audit logs.
```

## Execution Workflow
1. Collect required inputs and mark unknowns as explicit assumptions.
2. Classify sync model (`local-first/server-first/hybrid`) and authority boundaries per entity.
3. Choose durable local storage for queue + cursors + tombstones based on platform/runtime constraints.
4. Define mutation queue schema, idempotency keys, ordering, and lock strategy.
5. Define optimistic update behavior and rollback triggers separate from durable sync state.
6. Define replay execution rules, retry classes, backoff/jitter, and stop conditions.
7. Define conflict detection signals (revision mismatch, conflict status codes, duplicate replay markers).
8. Define merge/reconciliation policy per entity and conflict severity path.
9. Define reconnect/bootstrap behavior (startup auth restore, queue resume, pull/replay ordering barrier).
10. Define observability events/metrics/redaction and operational alert thresholds.
11. Produce structured output using the Output Contract exactly.

## Edge Cases
- App killed with pending queue entries before replay commit.
- Duplicate replay after reconnect due to uncertain prior ACK.
- Same stale entity edited on two devices and synced later.
- Delete vs update conflict for same entity.
- Server accepts subset of batch mutations, rejects remainder.
- Auth token expires during replay loop.
- Retry storm after network restoration across many queued mutations.
- Queue persisted across app update but local schema version changed.
- Optimistic UI shows success while durable replay eventually fails.
- Device clock skew distorts timestamp-based merge rules.
- User logs out with pending offline mutations.
- App restored from device backup with stale queue against new backend state.
- Websocket push arrives while replay for same entity is still in progress.
- Corrupted queue record payload cannot deserialize at startup.
- Connectivity flaps rapidly, causing replay start/stop thrashing.

## Observability
Emit structured events with correlation IDs (`sync_session_id`, `mutation_id`, `entity_type`, `entity_id`) and redacted metadata only.

- `offline_queue_enqueue`
- `offline_queue_replay_start`
- `offline_queue_replay_success`
- `offline_queue_replay_failure`
- `sync_conflict_detected`
- `sync_conflict_resolved`
- `optimistic_write_rolled_back`
- `auth_expired_during_sync`
- `queue_reset`
- `durable_sync_failure`

Rules:
- Never log tokens, secrets, or raw sensitive record payloads unless explicitly redacted.
- Include failure class (`network`, `auth`, `conflict`, `validation`, `storage`) for every replay failure event.
- Track queue depth, oldest pending age, replay latency, conflict rate, and permanent-failure count.

## Output Contract
Return all sections in this exact order:

1. Context Summary
2. Assumptions
3. Sync Model Choice
4. Authority Boundaries
5. Local Storage Choice
6. Mutation Queue Design
7. Replay / Retry Rules
8. Conflict Detection Rules
9. Merge / Reconciliation Policy
10. Delete Handling Policy
11. Failure Handling Plan
12. Observability Events
13. Verification Checklist
14. Risks / Rollback
15. Next Implementation Step

## Verification Checklist
- Queue is durable across process kill/restart and device reboot where platform allows.
- Replay uses idempotency keys and rejects duplicate side effects.
- Ordering is deterministic for mutations targeting same entity.
- Conflict detection is implemented with explicit revision or server conflict signals.
- Merge policy exists per entity type and matches authority boundaries.
- Delete intent is persisted and not lost during offline periods.
- Optimistic state rollback path exists for durable replay failure.
- Auth-expiry during replay is handled without queue corruption.
- Partial batch failure handling is deterministic and observable.
- Telemetry emits required events with redaction policy enforced.
- Reconnect/bootstrap flow avoids replay/pull race conditions.
- Schema migration path for persisted queue is defined and tested.

## Risks / Rollback
- Risk: incorrect merge policy causes silent data loss.
  - Rollback: switch entity to server-authoritative mode and disable local auto-merge.
- Risk: queue migration bug corrupts pending mutations.
  - Rollback: stop replay, snapshot corrupted queue, run controlled queue reset with user-visible resync.
- Risk: retry storm impacts backend stability.
  - Rollback: increase backoff, apply global replay throttle, temporarily pause replay via remote flag.
- Risk: optimistic success diverges from durable sync result.
  - Rollback: enforce rollback UI state and force reconciliation pull before allowing further edits.

## Example Request
```text
Platform mode: Expo Managed
Local storage layer: expo-sqlite
Cache/query layer: @tanstack/react-query
Backend sync model: REST
Source of truth: hybrid
Entity types: tasks, task_comments
Conflict tolerance: tasks=medium, task_comments=low
Offline write support: yes
Optimistic update policy: staged optimistic
Auth/session dependency: short-lived JWT + refresh token
Retry constraints: max 7 attempts, exponential backoff with jitter, wifi/cellular allowed
Reconciliation ownership: hybrid
```

## Example Response Shape
```text
Context Summary
- ...

Assumptions
- ...

Sync Model Choice
- ...

Authority Boundaries
- ...

Local Storage Choice
- ...

Mutation Queue Design
- ...

Replay / Retry Rules
- ...

Conflict Detection Rules
- ...

Merge / Reconciliation Policy
- ...

Delete Handling Policy
- ...

Failure Handling Plan
- ...

Observability Events
- ...

Verification Checklist
- ...

Risks / Rollback
- ...

Next Implementation Step
- ...
```
