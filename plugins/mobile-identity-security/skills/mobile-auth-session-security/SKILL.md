---
name: mobile-auth-session-security
description: Execution-grade skill for designing secure token lifecycle and session integrity in React Native and Expo apps.
metadata:
  domain: mobile-identity-security
---

# Skill: mobile-auth-session-security

## Purpose

Design, implement, and harden authentication session architecture for **Expo** and **React Native** applications.

This skill guides an AI agent through the full lifecycle of secure mobile authentication including:

- session architecture selection
- token lifecycle design
- secure token storage
- refresh token safety
- session revocation
- biometric re-authentication boundaries
- provider SDK integration
- failure recovery

The objective is to produce **secure, deterministic, and production-grade mobile authentication behavior** that remains reliable under real mobile conditions such as:

- app termination
- background resume
- network loss
- token expiration
- concurrent refresh requests
- multi-device sessions

This skill must always prefer **security, determinism, and maintainability over convenience**.

---

## When to Use

Use this skill when:

- implementing authentication systems in Expo or React Native apps
- designing mobile session lifecycle and token handling
- integrating OAuth or provider-based login
- implementing secure token storage
- adding refresh token rotation or renewal flows
- implementing biometric unlock for authenticated sessions
- designing logout and revocation behavior
- hardening an existing authentication flow
- adding offline-aware session handling
- debugging unstable mobile authentication behavior

---

## When Not to Use

Do not use this skill when:

- authentication occurs entirely in a browser
- the task only concerns UI login screens
- backend identity architecture is being designed without mobile responsibilities
- the mobile app does not manage authentication state
- the task concerns purely backend identity services

---

## Required Inputs

Before executing this skill the agent must collect the following inputs.

### Platform

- Expo Managed
- Expo + EAS
- Bare React Native

### Target Platforms

- iOS
- Android
- Both

### Authentication Provider

- Firebase
- Supabase
- Auth0
- Clerk
- Custom Backend
- Other OAuth provider

### Authentication Method

- Email/password
- OAuth
- Magic link
- OTP
- SSO

### Backend Session Model

- JWT
- Opaque session
- Hybrid

### Refresh Strategy

- Rotating refresh tokens
- Non-rotating refresh tokens
- Provider-managed refresh

### Security Requirements

- Standard
- High-risk
- Regulated

### Offline Support

- None
- Limited
- Required

### Biometric Unlock

- Required
- Optional
- Not used

### Deep Link Redirect

- Required
- Not required

If these inputs are incomplete, the agent must explicitly declare assumptions before proceeding.

---

## Framework-Specific Directives

### Expo Managed

Recommended libraries:

- `expo-secure-store` for refresh token persistence and other sensitive secrets
- `expo-auth-session` for OAuth and redirect-based authentication

Rules:

- Never store authentication tokens in `AsyncStorage`
- Use redirect-based auth flows for OAuth providers
- Avoid native modules incompatible with Expo runtime
- Keep access tokens in memory when possible
- Persist only the minimum long-lived secret required for session restoration

Example installation:

```bash
npx expo install expo-secure-store expo-auth-session
```

### Expo + EAS

Native modules may be used when supported by the EAS build pipeline.

Secure storage options:

- `expo-secure-store`
- `react-native-keychain`

Rules:

- Prefer Expo-compatible primitives unless a native dependency provides a clear security or platform benefit
- Verify native module compatibility before choosing a storage layer

### Bare React Native

Preferred secure storage libraries:

- `react-native-keychain`
- native iOS Keychain / Android Keystore wrappers

Installation example:

```bash
npm install react-native-keychain
```

Avoid:

- `AsyncStorage` for tokens
- plaintext persistence
- custom crypto wrappers unless there is a strong reason and key management is clearly defined

---

## Provider-Specific Directives

### Supabase

- Use Supabase auth state listeners
- Respect Supabase refresh lifecycle ownership
- Do not duplicate provider-managed refresh logic
- Ensure local auth state derives from provider session events, not only cached token presence

### Firebase

- Use Firebase Auth state observers
- Avoid manual token refresh if Firebase SDK handles it
- Do not build a parallel custom session state source that can drift from Firebase state

### Auth0

- Follow Auth0 session expiration and logout semantics
- Align local credential clearing with provider session clearing
- Treat browser and provider session state separately from local app unlock state

### Clerk

- Align local session handling with Clerk SDK ownership
- Avoid overriding provider refresh or multi-session behavior without a strong reason

### Custom Backend

Agent must explicitly define:

- token issuance
- expiration policy
- refresh rotation
- revocation logic
- replay protection
- device-scoped versus global logout semantics

Custom backends **must implement refresh race protection** and explicit 401 recovery behavior.

---

## Technical Implementation Patterns

### Auth Context Pattern

Applications should centralize authentication state in a single provider or session manager.

Example structure:

```text
AuthProvider
 ├── login()
 ├── logout()
 ├── refreshSession()
 ├── restoreSession()
 ├── getAccessToken()
 └── authState
```

Responsibilities:

- hold authentication state
- restore persisted session safely on app launch
- manage refresh logic
- protect against refresh race conditions
- expose a single source of truth for auth transitions

### Refresh Guard

Refresh requests must use **single-flight protection**.

Required behavior:

- if refresh is already in progress, await the existing refresh promise
- never start multiple refresh requests for the same session concurrently
- if refresh fails with an unrecoverable auth error, clear local session state and force re-authentication

### Session Storage Rules

Access token:

- store in memory by default
- persist only if required by the provider SDK and only in secure storage

Refresh token:

- store in secure storage only
- never expose to UI components
- never cache in logs, analytics payloads, or debug screens

Session metadata:

- may include expiry timestamps, user ID, and provider markers
- must not be treated as proof of authentication without server- or provider-valid session state

---

## Anti-Patterns

The following patterns are strictly prohibited:

- storing tokens in `AsyncStorage`
- refreshing tokens in multiple parallel requests
- using token presence as the only auth state signal
- ignoring backend revocation events
- exposing refresh tokens to UI code
- assuming biometric unlock proves backend session validity
- silently swallowing refresh failures and leaving stale auth state active

---

## Decision Tree

### Platform

If **Expo Managed** → use Expo secure storage and `expo-auth-session` compatible flows.

If **Expo + EAS** → use Expo-compatible flows by default, then permit native modules only when build compatibility and security justification are clear.

If **Bare React Native** → use native keychain/keystore-backed storage and native-capable auth integrations where appropriate.

### Auth Provider

If **provider-based auth** → align lifecycle with provider SDK ownership, listeners, refresh behavior, and logout semantics.

If **custom backend** → define explicit token lifecycle, revocation rules, retry semantics, and failure behavior.

### Security Level

If **high-risk** or **regulated** → require:

- shorter token lifetime
- tighter re-authentication boundaries for sensitive actions
- stricter revocation handling
- less tolerance for offline authenticated behavior

### Offline Behavior

If offline support is required:

- define a bounded cached-session TTL
- fail closed when session integrity is uncertain
- refresh once connectivity returns before resuming privileged operations

### Biometric Unlock

If biometric unlock is enabled:

- use it only to unlock locally stored session material or gate sensitive screens
- do not treat biometric success as equivalent to backend session validity

---

## Execution Workflow

1. Collect required inputs.
2. Identify platform and build constraints.
3. Identify provider ownership of session lifecycle.
4. Define the threat model.
5. Choose the session architecture.
6. Define access-token and refresh-token lifecycle rules.
7. Define secure storage boundaries.
8. Model explicit auth states and transitions.
9. Implement restore-session flow for app launch and resume.
10. Implement refresh guard and failure behavior.
11. Define logout and revocation handling.
12. Define offline behavior and privileged-action gating.
13. Define biometric unlock boundary if applicable.
14. Add observability and diagnostics.
15. Produce structured output and verification notes.

---

## Edge Cases

The skill must handle the following scenarios:

- app killed during OAuth redirect
- app terminated during refresh
- multiple refresh requests triggered concurrently
- revoked session on another device
- token expires while offline
- network loss during refresh
- device clock skew causes premature expiry logic
- secure storage unavailable
- corrupted secure storage
- deep link redirect interruption or duplicate callback
- refresh token rotation mismatch
- biometric settings changed after enrollment
- app reinstall with active backend session
- logout during refresh
- restored local token after backend session was already revoked

Each case must produce a deterministic recovery path.

---

## Observability

Authentication systems must include diagnostic signals.

Recommended events:

- login success
- login failure
- session restore success
- session restore failure
- refresh attempt
- refresh success
- refresh failure
- logout
- session revoked
- forced re-authentication

Rules:

- never log raw tokens or secrets
- log enough metadata to debug state transitions and repeated failures
- distinguish provider failures, storage failures, and network failures

---

## Output Contract

The agent must produce output containing:

1. Context Summary
2. Assumptions
3. Chosen Session Model
4. Platform Constraints
5. Threat Model
6. Token Lifecycle Plan
7. Storage Policy
8. Auth State Machine
9. Restore Session Strategy
10. Refresh Strategy
11. Offline Behavior
12. Biometric Re-authentication Policy
13. Failure Handling Strategy
14. Verification Checklist
15. Risks and Rollback
16. Next Implementation Step

---

## Verification Checklist

- secure storage is used for persisted secrets
- refresh guard is implemented
- auth state machine is explicit
- restore-session flow is defined
- logout clears local authenticated state immediately
- revoked sessions invalidate local state
- offline behavior is explicitly bounded
- biometric unlock is separated from backend session validity
- redirect interruptions are handled
- sensitive secrets are never logged

---

## Risks / Rollback

Potential risks:

- insecure token storage
- refresh loops
- stale local sessions
- revocation mismatch
- provider and local session state drift

Rollback strategy:

1. clear secure storage
2. reset in-memory auth state
3. force user re-authentication
4. temporarily disable unstable refresh logic
5. fall back to a simpler session model if migration integrity is uncertain

---

## Example Request

Platform: Expo Managed  
Provider: Supabase  
Auth Method: Email/Password  
Session Model: JWT  
Offline Support: Limited  
Biometric Unlock: Optional  
Security Level: Standard

---

## Example Response Shape

### Context Summary

Expo managed application using Supabase authentication.

### Chosen Session Model

Provider-managed JWT session with provider-owned refresh lifecycle.

### Storage Policy

Access token stored in memory.  
Refresh token persisted in `expo-secure-store`.

### Auth State Machine

Unauthenticated → Authenticating → Authenticated → Refreshing → Expired → Revoked.

### Restore Session Strategy

Restore session on app launch from secure storage, then validate provider session before enabling privileged actions.

### Refresh Strategy

Single-flight refresh guard. No parallel refresh attempts.

### Failure Handling

Refresh failure or revoked session forces local session reset and re-authentication.

### Next Implementation Step

Implement `AuthProvider`, session restore flow, and single-flight refresh guard.