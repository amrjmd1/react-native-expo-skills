---
name: mobile-api-query-architecture
description: Execution-grade skill for designing API query layers and caching strategies in React Native and Expo apps.
metadata:
  domain: mobile-data-platform
---

# Skill: mobile-api-query-architecture

## Purpose
Design deterministic API query and mutation architecture for React Native and Expo apps, covering request orchestration, caching, retry policy, stale-data handling, pagination, cancellation, and resilient recovery under unstable mobile networks.

## When to Use
- Building or hardening a mobile data access layer on REST, GraphQL, or hybrid APIs.
- Defining query caching behavior, invalidation rules, and background refetch policy.
- Implementing optimistic updates with rollback and mutation side-effect isolation.
- Fixing retry storms, stale cache bugs, request races, or pagination consistency issues.
- Establishing request cancellation, error taxonomy, and rate-limit-aware client behavior.

## When Not to Use
- Pure backend API design with no mobile client query/caching scope.
- UI-only state management not backed by remote data.
- One-off endpoint debugging without persistent architecture decisions.

## Required Inputs
Collect these inputs before implementation. Missing values must be explicit assumptions.

- API architecture: `REST` | `GraphQL` | `hybrid`.
- Query client library: `TanStack Query` | `RTK Query` | custom.
- Transport layer: `fetch` wrapper | `Axios` | GraphQL client.
- Backend consistency model: strong | eventual | mixed; server conflict/versioning behavior.
- Offline behavior requirements: read-only cache vs offline writes vs queued mutations.
- Pagination model: offset/page | cursor | keyset | mixed.
- Mutation side effects: dependent queries/entities touched per mutation.
- Cache invalidation strategy: tag-based | key-prefix | event-driven | manual.
- Network retry policy: retryable error classes, max attempts, backoff+jitter, timeouts.
- Authentication coupling: token refresh flow, request replay rules after refresh.
- Rate limiting constraints: quotas, `429` handling, server retry headers.
- Error classification model: transport, auth, validation, conflict, throttling, server, unknown.
- Environment constraints: dev/staging/prod endpoints, feature flags, canary rollout behavior.

## Framework-Specific Directives
### Expo Managed
- Prefer `@tanstack/react-query` for query/mutation orchestration with explicit `staleTime`, `gcTime`, and focus/reconnect behavior.
- Use `fetch` wrapper or `Axios` instance with centralized auth, timeout, and error normalization.
- Use `@react-native-community/netinfo` to gate reconnect refetch and avoid flood refetch loops.
- Persist cache only when required; separate durable mutation queue from UI cache state.

### Expo + EAS
- Follow Expo Managed directives plus environment-safe base URL/key selection per EAS profile.
- Enforce release-channel-aware query defaults (e.g., tighter logging in preview, stricter retry caps in production).
- Validate sourcemaps/release metadata for API error correlation during incident triage.
- Use remote config/feature flags for emergency retry/invalidation tuning without rebuild.

### Bare React Native
- Use `TanStack Query` or `RTK Query` with transport abstraction (Axios/fetch) and native-aware cancellation (`AbortController`).
- For high-volume/offline use cases, use SQLite/Realm-backed queue for durable mutation replay.
- Avoid running concurrent duplicate workers on reconnect; enforce single replay coordinator.
- Account for app lifecycle/background limits; background refetch is best-effort, not guaranteed.

## Provider-Specific Directives
### REST
- Normalize endpoint contracts into typed domain DTO mappers.
- Derive stable cache keys from resource identity + normalized params.
- Use ETag/If-None-Match or version headers when available for stale-data control.

### GraphQL
- Prefer normalized entity identity (`__typename:id`) and deterministic variable serialization for cache keys.
- Separate query/mutation documents by feature domain; avoid monolithic query blobs.
- Treat partial data + errors explicitly; do not treat partial response as full success by default.

### Hybrid
- Define one canonical entity identity model across REST and GraphQL sources.
- Centralize invalidation events so mutation side effects invalidate both data planes deterministically.

## Technical Implementation Patterns
- Query client architecture:
  - Layering: transport client -> API adapters -> query/mutation hooks -> UI selectors.
  - Keep transport concerns (auth, timeout, retry hints) out of UI hooks.
  - Enforce per-screen request budgets (concurrency + burst limits) so UI event cascades cannot trigger unbounded refetches.
  - Route screen-level data needs through shared query client keys to dedupe requests and apply budget gates consistently.
- Cache key design:
  - Use stable, deterministic keys (`[resource, id, normalizedParams]`).
  - Normalize optional params and sort object keys to avoid accidental cache misses.
- Background refetch policy:
  - Define per-query `staleTime` and reconnect/focus refetch rules by data criticality.
  - Rate-limit reconnect refetch using jittered scheduling.
- Optimistic update flow:
  - Snapshot previous cache state, apply optimistic patch, rollback on failure, then reconcile with server response.
  - Track mutation version/sequence to avoid older responses overwriting newer optimistic state.
- Mutation side-effect isolation:
  - Explicitly list affected query keys/tags per mutation.
  - Invalidate minimally; prefer targeted cache updates over global refetch.
- Request deduplication:
  - Coalesce identical in-flight queries by key.
  - Use idempotency keys for mutations where backend supports dedupe.
- Endpoint circuit breaker pattern:
  - Track consecutive failures or elevated error rate per endpoint within a rolling window.
  - Open breaker to temporarily stop new requests, apply cooldown, then use half-open probes to verify recovery.
  - Prevent retry storms and cascading failures during backend instability.
- Pagination cursors:
  - Store cursor tokens per query key and invalidate cursor chain when filters/sorts change.
  - Prevent page merge duplicates by identity-based dedupe on append.
- Stale data handling:
  - Display stale markers when serving cache beyond SLA.
  - Trigger background refresh while preserving responsive UI.

## Anti-Patterns
- Uncontrolled refetch loops from focus/reconnect state changes.
- Cache keys built from unstable objects/functions.
- Retry storms after reconnect or auth refresh failures.
- Mixing ephemeral UI state and server query cache as one source of truth.
- Ignoring pagination consistency when sort/filter changes.
- Allowing older mutation responses to overwrite newer client state.
- Global invalidation on every mutation without side-effect scope.
- Retrying non-retryable errors (`4xx` validation/conflict) indefinitely.

## Decision Tree
```text
If API architecture == REST
  -> use resource-oriented cache keys + endpoint-level invalidation rules.
Else if API architecture == GraphQL
  -> use operation+variables keys and entity identity normalization.
Else (hybrid)
  -> define unified entity identity and cross-source invalidation bus.

If data criticality requires freshness
  -> network-first with short staleTime and bounded refetch policy.
Else
  -> cache-first with background refresh.

If mutation has low conflict risk and reversible side effects
  -> optimistic update with rollback snapshot.
Else
  -> pessimistic flow (await server ack before cache commit).

If pagination model == cursor/keyset
  -> maintain per-key cursor chain and identity dedupe across pages.
Else (offset/page)
  -> enforce deterministic page invalidation on mutation/filter changes.

If error class is transient (network/5xx/429)
  -> retry with exponential backoff + jitter + max attempts.
Else
  -> fail fast and surface normalized error to UI/workflow.
```

## Execution Workflow
1. Collect required inputs and document assumptions.
2. Define API interaction model (REST/GraphQL/hybrid boundaries).
3. Define query client architecture and transport abstraction.
4. Define caching strategy (keys, stale windows, invalidation scope).
5. Define mutation handling (optimistic/pessimistic, side-effect map, rollback).
6. Define retry/backoff/timeout policy by error class and endpoint criticality.
7. Define background refresh rules (focus, reconnect, interval, cancellation).
8. Define error classification and UI/domain error contracts.
9. Validate resilience under network instability, auth expiry, and rate limiting.
10. Produce output using the Output Contract exactly.

## Edge Cases
- Pagination inconsistency after item insert/delete between page loads.
- Stale cache survives mutation because affected keys were not invalidated.
- Reconnect causes request flood and duplicate refetches.
- Duplicate mutation replay after uncertain network ACK.
- Server partial failure returns mixed success/errors in batch mutation.
- Token expires mid-request while multiple queries are in-flight.
- Race conditions between concurrent queries updating same entity.
- Slow API responses block UI due to missing cancellation/loading boundaries.
- `429` bursts trigger retries without honoring server retry windows.
- Background refetch overwrites local optimistic state before mutation settles.

## Observability
Track bounded, actionable client data-layer signals:
- `api_request_rate`
- `api_error_rate`
- `retry_rate`
- `request_latency`
- `cache_hit_rate`
- `cache_stale_ratio`
- `inflight_request_count`
- `pagination_duplicate_item_rate`

Rules:
- Include `environment`, `platform`, `endpoint_group`, `release` dimensions only.
- Never log sensitive payload bodies or auth tokens.
- Track retry cause distribution and cancellation counts for tuning.

## Output Contract
Return sections in this exact order:

1. Context Summary
2. Assumptions
3. API Interaction Model
4. Query Client Architecture
5. Caching Strategy
6. Mutation Handling
7. Retry Policy
8. Pagination Strategy
9. Error Handling
10. Verification Checklist
11. Risks / Rollback
12. Next Implementation Step

## Verification Checklist
- API interaction model and transport boundaries are explicitly defined.
- Query keys are deterministic and stable for identical requests.
- StaleTime/refetch rules prevent both stale lock-in and refetch thrash.
- Mutation side effects map to explicit invalidation/update rules.
- Optimistic updates include rollback and race protection.
- Retry policy is bounded and class-aware (`429/5xx` vs non-retryable `4xx`).
- Request cancellation works for screen transitions/unmount.
- Pagination merges avoid duplicates and maintain ordering consistency.
- Auth refresh flow avoids duplicate replay and infinite retry loops.
- Observability signals are emitted and privacy-safe.

## Risks / Rollback
- Risk: aggressive invalidation increases network load and UI jitter.
  - Rollback: scope invalidation to affected keys and raise stale windows.
- Risk: optimistic updates cause visible state divergence.
  - Rollback: switch affected mutations to pessimistic mode temporarily.
- Risk: retry tuning causes server/client pressure during outages.
  - Rollback: reduce max retries, add reconnect jitter, and enforce global retry cap.
- Risk: pagination corruption in production feed.
  - Rollback: reset pagination cache for impacted keys and force fresh bootstrap fetch.

## Example Request
```text
API architecture: REST
Query client library: TanStack Query
Backend consistency model: eventual with version headers
Offline behavior: cached reads + queued writes for selected endpoints
Pagination model: cursor
Mutation side effects: task updates affect task list, task detail, dashboard counters
Cache invalidation: key-prefix + targeted patch updates
Network retry policy: 3 retries, exponential backoff, jitter, 429 aware
Authentication coupling: JWT + refresh token replay once
Rate limiting constraints: 100 requests/min
Error classification: transport/auth/validation/conflict/server
```

## Example Response Shape
```text
Context Summary
- ...

Assumptions
- ...

API Interaction Model
- ...

Query Client Architecture
- ...

Caching Strategy
- ...

Mutation Handling
- ...

Retry Policy
- ...

Pagination Strategy
- ...

Error Handling
- ...

Verification Checklist
- ...

Risks / Rollback
- ...

Next Implementation Step
- ...
```
