---
name: mobile-auth-session-security
description: Execution-focused mobile authentication and session security skill for React Native and Expo. Use for choosing auth/session model, token lifecycle design, secure storage boundaries, auth state machine behavior, refresh/revocation handling, and failure-mode controls across managed Expo, Expo with EAS, and bare React Native apps.
---

# Skill: mobile-auth-session-security

## Purpose
Design and implement production-safe mobile authentication session architecture with deterministic token handling, storage boundaries, and failure recovery.

## When to Use
- Implementing or refactoring login/session flows in React Native or Expo.
- Choosing between JWT, opaque sessions, provider-managed sessions, or hybrid models.
- Designing refresh token rotation, revocation, and multi-device logout behavior.
- Defining secure token storage and biometric re-auth boundaries.
- Debugging auth race conditions, session drops, or inconsistent login state.

## When Not to Use
- No authentication/session state exists (public-only app).
- Task is only UI styling of auth screens.
- Task is backend-only auth implementation with no mobile client impact.
- Task is generic API error handling unrelated to session/token lifecycle.

## Required Inputs
- Platform type: Expo managed, Expo + EAS/prebuild, or bare React Native.
- Auth provider: Supabase, Auth0, Firebase, Cognito, custom backend, other.
- Backend session model: JWT, opaque session, rotating refresh token, provider SDK session.
- Token storage approach: SecureStore/Keychain/Keystore/MMKV/other.
- Offline requirements: read-only offline, mutating offline, no offline.
- Biometric re-auth requirement: none, optional, required for sensitive actions.
- App risk level: low, medium, high (finance/health/admin = high).
- Multi-device session policy: allow, revoke all, selective revoke.

## Assumptions
- Strict TypeScript is required.
- Mobile client is untrusted; backend enforces authorization.
- Access token is short-lived.
- Session transitions must be explicit state-machine transitions.
- Secure storage APIs are available for the target platform.

## Decision Tree
- If Expo managed, then use Expo-compatible secure storage and avoid native-only auth dependencies unless explicitly approved.
- If Expo + EAS/prebuild, then allow config-plugin/native additions when provider SDK requires them.
- If bare React Native, then allow direct native SDK integration and custom keychain/keystore controls.
- If provider is Supabase/Auth0/Firebase, then use provider session primitives first and wrap them in a typed client state machine.
- If provider is custom backend with JWT + refresh, then enforce rotating refresh token flow and refresh replay protection.
- If backend session model is opaque session, then store only session handle/cookie material and avoid client-side JWT parsing assumptions.
- If app risk level is high, then require biometric gate for sensitive operations and enforce aggressive session timeout/re-auth policies.
- If offline access is required, then define exactly what can be used while token is expired and block privileged mutations until revalidation.
- If multi-device revoke is required, then implement forced re-auth on revoke event/poll check and clear local credentials immediately.

## Execution Workflow
1. Context assessment: confirm platform, provider, backend model, and risk tier.
2. Threat modeling: enumerate token theft, refresh replay, device compromise, and deep-link interruption risks.
3. Session architecture choice: select JWT/opaque/provider model and define client responsibilities.
4. Token lifecycle design: define issue/expiry/refresh/rotation/revocation timeline and retry windows.
5. Secure storage boundaries: map what is stored in secure storage vs memory-only vs derived cache.
6. Auth state machine design: define explicit states (unauthenticated, authenticating, authenticated, refreshing, reauth-required, revoked, error).
7. Refresh/revocation handling: enforce single-flight refresh, replay-safe rotation, and deterministic revoke logout.
8. Failure handling: define behavior for offline expiry, invalid refresh, provider outage, and clock skew.
9. Verification: run scenario-based checks for lifecycle, race conditions, and forced logout behavior.

## Edge Cases
- App killed during refresh operation.
- Multiple parallel refresh attempts from concurrent API calls.
- Session revoked from another device while app is active.
- Access token expired while offline with pending mutations.
- App reinstall with stale active backend session.
- Biometric enrollment/passcode changed after secure storage setup.
- Device clock skew causing premature token expiry checks.
- Deep link / OAuth redirect interrupted before session finalization.

## Output Contract
Return output in this exact shape:
- Chosen auth/session model
- Assumptions
- Platform constraints
- State machine summary
- Storage policy
- Failure handling strategy
- Verification checklist
- Risks / rollback plan
- Next implementation step

## Verification Checklist
- Session model selected and justified for platform/provider.
- Access and refresh token lifetimes explicitly defined.
- Refresh logic is single-flight and race-safe.
- Revocation path forces deterministic local logout.
- Secure storage policy defined for every secret/session artifact.
- Offline behavior for expired sessions explicitly defined.
- Biometric policy defined for sensitive operations.
- Auth state machine transitions are complete and non-ambiguous.
- Failure scenarios tested: refresh failure, revoke, clock skew, deep-link interruption.

## Risks / Rollback
- Risk: refresh loop causes repeated auth failures and API storm.
  - Rollback: disable auto-refresh, force re-auth, and ship hotfix with single-flight guard.
- Risk: stale local session after backend revoke.
  - Rollback: enforce startup/session-introspection check and immediate credential purge.
- Risk: secure storage migration breaks existing sessions.
  - Rollback: version storage schema, force one-time re-login, preserve user data cache separately.
- Risk: provider SDK update changes session callback behavior.
  - Rollback: pin provider SDK version and restore previous auth callback handling.

## Example Request
"Expo managed app with Supabase auth. Need rotating refresh token strategy, secure storage policy, biometric re-auth for payout actions, and handling for offline token expiry."

## Example Response Shape
- Chosen auth/session model:
  - Supabase provider session with short-lived access token + rotating refresh token.
- Assumptions:
  - Expo managed workflow, medium-high risk app, payout actions require re-auth.
- Platform constraints:
  - Use Expo-compatible secure storage; avoid native-only modules.
- State machine summary:
  - `unauthenticated -> authenticating -> authenticated -> refreshing -> authenticated`
  - `authenticated -> reauth-required` for sensitive action gate.
  - `any -> revoked -> unauthenticated` on revoke signal.
- Storage policy:
  - Refresh token in secure storage.
  - Access token memory-only.
  - Biometric gate token only for sensitive-action unlock window.
- Failure handling strategy:
  - Single-flight refresh; queued requests await result.
  - On refresh invalid/revoked: clear storage and force login.
  - On offline expiry: allow read-only cached view, block privileged mutations.
- Verification checklist:
  - Parallel refresh test
  - Revoke-from-second-device test
  - App-kill-during-refresh test
  - Clock-skew simulation test
- Risks / rollback plan:
  - Feature-flag new refresh logic.
  - Instant fallback to forced-login strategy on elevated auth errors.
- Next implementation step:
  - Implement typed auth state machine and refresh single-flight guard in auth service.
