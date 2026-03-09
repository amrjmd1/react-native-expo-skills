---
name: mobile-biometric-secure-storage
description: Execution-grade skill for secure local secret handling and biometric re-auth in React Native and Expo apps. Use for storage boundary design, biometric gate policy, fallback auth flow, lifecycle failure handling, and platform-specific hardening across Expo managed, Expo + EAS, and bare React Native.
---

# Skill: mobile-biometric-secure-storage

## Purpose
Implement and verify secure local credential handling with biometric re-auth controls that remain correct across app restarts, offline states, biometric changes, and device trust changes.

## When to Use
- Designing token/secret storage policy on mobile clients.
- Adding biometric re-auth for sensitive actions or session unlock.
- Migrating storage implementation between Expo and bare RN.
- Fixing auth breakage after app reinstall, biometric changes, or secure storage failures.
- Hardening client-side secret handling in high-risk apps.

## When Not to Use
- Backend-only auth/session work with no mobile storage implications.
- UI-only biometric prompt styling.
- Non-sensitive local preferences storage.
- Full auth/session architecture decisions (use `mobile-auth-session-security`).

## Required Inputs
- Platform mode: `expo-managed` | `expo-eas-prebuild` | `rn-bare`.
- Secret classes: refresh token, session handle, key material, local unlock marker.
- Risk tier: `low` | `medium` | `high`.
- Biometric policy: `none` | `sensitive-actions` | `session-unlock`.
- Offline mode: `none` | `read-only` | `mutations-queued`.
- Re-auth timeout: inactivity threshold and max unlock window.
- Device trust policy: rooted/jailbroken handling and block/allow behavior.
- Auth provider type: `supabase` | `auth0` | `firebase` | `cognito` | `custom`.

## Framework-Specific Directives
- `expo-managed`
  - Use `expo-secure-store` for encrypted secret storage.
  - Use `expo-local-authentication` for biometric checks.
  - Do not require native-only keychain flags unavailable in managed workflow.
- `expo-eas-prebuild`
  - Start with Expo APIs; add native modules only when policy requires missing controls.
  - Document config plugin/native changes explicitly.
- `rn-bare`
  - Use `react-native-keychain` for advanced keychain/keystore controls.
  - Use `react-native-biometrics` or platform-native bridge only when required by policy.

## Provider-Specific Directives
- `supabase` / `auth0` / `cognito`
  - Store refresh/session artifacts only in secure storage.
  - Keep access token memory-only where possible.
- `firebase`
  - Treat Firebase persistence settings explicitly; do not assume secure enclave behavior for all artifacts.
- `custom`
  - Define exact artifact mapping: what is encrypted at rest, what is memory-only, what is never persisted.

## Technical Implementation Patterns
- Implement a `SecureCredentialService` with strict methods:
  - `saveSecret(secretType, value)`
  - `readSecret(secretType)`
  - `deleteSecret(secretType)`
  - `clearAllSecrets()`
- Implement a separate `BiometricGateService`:
  - `canEvaluateBiometrics()`
  - `promptReauth(reason)`
  - `invalidateLocalTrust()`
- Store policy table per secret class:
  - storage backend
  - biometric requirement
  - retention window
  - wipe trigger
- Example storage policy:
  - `refreshToken -> secure storage -> biometric gate (sensitive actions) -> wipe on revoke`
  - `accessToken -> memory-only -> no persistence`
  - `localUnlockMarker -> secure storage -> expires on restart/background timeout`
- Use versioned storage schema for migrations.
- Enforce single source of truth for local trust state.

## Anti-Patterns
- Storing refresh tokens in AsyncStorage or plaintext files.
- Logging secret values or raw biometric errors with sensitive context.
- Treating biometric success as backend authorization.
- Keeping long-lived unlock state without timeout.
- Running concurrent biometric prompts from multiple UI paths.
- Silent fallback to insecure storage on secure-store failure.

## Decision Tree
- If `platform=expo-managed`, then use `expo-secure-store` + `expo-local-authentication` only.
- If policy needs keychain access-control flags unavailable in managed Expo, then use `expo-eas-prebuild` or `rn-bare` path.
- If `risk=high`, then require biometric re-auth for sensitive actions and short unlock timeout.
- If `biometric policy=session-unlock`, then keep unlock marker ephemeral and invalidate on app restart/background timeout.
- If `offline=mutations-queued`, then allow queueing only after local trust check; block flush when trust invalid.
- If biometric enrollment/passcode changes are detected, then invalidate local trust and require full credential re-entry.
- If secure storage read fails or returns corrupt payload, then purge local secrets and force re-auth.

## Execution Workflow
1. Collect required inputs and classify secret types.
2. Define storage policy table (secret -> storage -> gate -> retention -> wipe trigger).
3. Choose framework-specific storage + biometric libraries by platform mode.
4. Implement `SecureCredentialService` and `BiometricGateService` with typed interfaces.
5. Implement trust lifecycle rules (create, refresh, expire, invalidate).
6. Add app lifecycle handling:
   - app restart
   - background/foreground timeout
   - storage read/write failure
7. Add biometric change and device trust-change invalidation path.
8. Add fallback flow: full credential re-auth when biometric or secure storage fails.
9. Instrument events and run verification checklist before completion.

## Edge Cases
- App terminated between secret write and trust-state update.
- Concurrent secret reads during token refresh.
- Biometric prompt interrupted by app backgrounding.
- Biometric enrollment changed after first setup.
- Device passcode removed after secure storage initialization.
- Reinstall leaves backend session active but local secrets wiped.
- Secure storage unavailable (OS error, keychain lock, corruption).
- Clock skew causing premature local unlock expiry.
- Offline mode with expired local unlock marker.
- Device restored from backup causing secure storage or keychain reset.
- Biometric system lockout after repeated failed attempts.
- OS-level secure enclave reset or keychain invalidation after OS update.

## Observability
- Emit events (no secrets):
  - `secure_storage_write_success|failure`
  - `secure_storage_read_success|failure`
  - `biometric_prompt_success|failure|cancelled`
  - `local_trust_invalidated`
  - `forced_reauth_triggered`
- Include metadata only:
  - platform mode
  - secret class (not value)
  - error code/category
  - app lifecycle state
- Never log secret values, raw tokens, biometric payloads, or PII.

## Output Contract
Return exactly these sections:
1. Context Summary
2. Assumptions
3. Framework Choice
4. Provider Constraints
5. Storage Policy Table
6. Biometric Gate Policy
7. Trust Lifecycle Rules
8. Failure Handling Plan
9. Observability Events
10. Verification Checklist
11. Risks / Rollback
12. Next Implementation Step

## Verification Checklist
- Secure storage backend is platform-correct and explicit.
- No sensitive artifact stored in insecure storage.
- Biometric gate policy matches risk tier and action sensitivity.
- Unlock timeout and invalidation triggers are implemented.
- Biometric change/passcode change invalidates trust.
- Storage corruption/unavailable path forces safe re-auth.
- App restart/background behavior follows policy.
- Concurrent access does not bypass trust checks.
- Observability events emitted without secrets.

## Risks / Rollback
- Risk: new storage migration causes mass sign-outs.
  - Rollback: revert storage schema version, force controlled re-auth, keep user data cache isolated.
- Risk: biometric prompt loop blocks app usage.
  - Rollback: disable biometric gate flag, switch to credential re-auth fallback.
- Risk: secure storage library regression after upgrade.
  - Rollback: pin last known-good version and disable new trust features.
- Risk: trust invalidation too aggressive increases friction.
  - Rollback: tune timeout/window via remote config while keeping high-risk actions gated.

## Example Request
"Expo managed app with Auth0: implement secure refresh-token storage, biometric re-auth for transfer actions, offline read-only behavior, and safe handling when biometric enrollment changes."

## Example Response Shape
- Context Summary:
  - platform: expo-managed
  - provider: auth0
  - risk: high
  - biometric policy: sensitive-actions
- Assumptions:
  - access token memory-only; refresh token persisted securely
- Framework Choice:
  - expo-secure-store + expo-local-authentication
- Provider Constraints:
  - Auth0 refresh artifact persisted; no token logging
- Storage Policy Table:
  - refreshToken -> secure store -> gated for transfer actions -> wipe on revoke/biometric change
  - accessToken -> memory-only -> no persistence
- Biometric Gate Policy:
  - required for transfer/withdraw actions
  - unlock window: 120 seconds
- Trust Lifecycle Rules:
  - invalidate on app restart + 5 min background timeout + biometric change
- Failure Handling Plan:
  - secure storage failure => clear local trust + force full login
  - biometric failure/cancel => deny sensitive action, keep session for non-sensitive routes
- Observability Events:
  - storage_read_failure, biometric_prompt_cancelled, forced_reauth_triggered
- Verification Checklist:
  - [scenario tests listed]
- Risks / Rollback:
  - feature flag to disable biometric gate if production incident occurs
- Next Implementation Step:
  - implement `SecureCredentialService` with versioned schema and typed error mapping.
