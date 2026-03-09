# Skill: mobile-auth-session-security

## Purpose

Design, implement, and harden authentication session architecture for mobile applications built with **Expo** or **React Native**. This skill ensures secure token storage, robust session lifecycle management, refresh token safety, revocation handling, biometric re‑authentication boundaries, and predictable behavior across iOS and Android environments.

This skill is intended for **production mobile applications** where authentication must be resilient, secure, and predictable under real-world conditions such as app termination, network loss, concurrent refresh attempts, and multi‑device sessions.

---

# When to Use

Use this skill when:

* Implementing authentication systems in Expo or React Native apps
* Designing mobile session lifecycle and token handling
* Integrating OAuth or provider-based login
* Implementing secure token storage
* Adding refresh token rotation or renewal flows
* Implementing biometric unlock for authenticated sessions
* Designing logout and revocation behavior
* Hardening an existing authentication flow
* Adding offline-aware session handling

---

# When Not to Use

Do not use this skill when:

* Authentication occurs entirely in a browser
* The task is only UI design for login screens
* Backend identity architecture is being designed without mobile client responsibilities
* The application does not persist or manage authentication state locally

---

# Required Inputs

Before executing this skill the agent must collect the following information.

## Platform

* Expo Managed
* Expo + EAS
* Bare React Native

## Target Platforms

* iOS
* Android
* Both

## Authentication Provider

* Firebase
* Supabase
* Auth0
* Clerk
* Custom Backend
* Other OAuth provider

## Authentication Method

* Email/password
* OAuth
* Magic link
* OTP
* SSO

## Backend Session Model

* JWT
* Opaque session
* Hybrid

## Refresh Strategy

* Rotating refresh tokens
* Non‑rotating refresh tokens
* Provider-managed refresh

## Security Requirements

* Standard
* High‑risk
* Regulated

## Offline Support

* None
* Limited
* Required

## Biometric Unlock

* Required
* Optional
* Not used

## Deep Link Redirect

* Required
* Not required

If these inputs are incomplete, the agent must explicitly state assumptions before continuing.

---

# Framework-Specific Directives

## Expo Managed

Preferred libraries:

* `expo-secure-store` for token persistence
* `expo-auth-session` for OAuth flows

Rules:

* Never store authentication tokens in `AsyncStorage`
* Prefer redirect-based authentication flows
* Avoid native modules incompatible with Expo runtime

---

## Expo + EAS

Additional native modules may be used if supported by the build pipeline.

Secure storage options:

* `expo-secure-store`
* `react-native-keychain` (if native modules are allowed)

---

## Bare React Native

Preferred secure storage libraries:

* `react-native-keychain`
* platform keystore/keychain APIs

Avoid:

* `AsyncStorage` for refresh tokens
* plaintext persistence

---

# Provider-Specific Directives

## Supabase

* Respect Supabase client session persistence
* Avoid duplicating refresh token lifecycle
* Use Supabase auth state listeners where possible

## Firebase

* Use Firebase Auth state observers
* Avoid manual token refresh logic when Firebase SDK manages it

## Auth0

* Align logout with Auth0 session clearing
* Respect provider token expiry semantics

## Custom Backend

Agent must define explicitly:

* token issuance
* expiration policy
* refresh rotation
* revocation logic
* replay protection

---

# Decision Tree

## Platform

If **Expo Managed** → use Expo-compatible secure storage and redirect flows.

If **Bare React Native** → use native keychain/keystore storage.

---

## Auth Provider

If **provider-based auth** → align session lifecycle with provider SDK.

If **custom backend** → define explicit token lifecycle and refresh strategy.

---

## Security Level

If **high-risk** → require:

* shorter token lifetimes
* biometric unlock for sensitive operations
* strict revocation handling

---

## Offline Behavior

If offline support required →

* allow cached session only within defined TTL
* force refresh when connectivity returns

---

# Execution Workflow

1. Collect required inputs
2. Detect platform constraints
3. Identify authentication provider
4. Define threat model
5. Choose session architecture
6. Define token lifecycle
7. Define secure storage boundaries
8. Define authentication state machine
9. Implement refresh logic with race protection
10. Implement logout behavior
11. Define offline session behavior
12. Define failure handling
13. Validate session integrity
14. Produce structured result

---

# Edge Cases

The skill must handle the following scenarios.

* App killed during OAuth redirect
* App terminated during refresh
* Multiple refresh requests triggered simultaneously
* Revoked session on another device
* Token expires while offline
* Network failure during refresh
* Device clock skew
* Secure storage unavailable
* Corrupted secure storage
* Deep link redirect interruption
* Refresh token rotation mismatch
* Biometric settings changed
* App reinstall with active server session
* Logout during refresh

Each case must have a deterministic recovery strategy.

---

# Output Contract

The agent must produce output containing:

1. Context Summary
2. Assumptions
3. Chosen Session Model
4. Platform Constraints
5. Threat Model
6. Token Lifecycle Plan
7. Storage Policy
8. Auth State Machine
9. Refresh Strategy
10. Offline Behavior
11. Biometric Re‑authentication Policy
12. Failure Handling Strategy
13. Verification Checklist
14. Risks and Rollback
15. Next Implementation Step

---

# Verification Checklist

* Secure storage used for tokens
* Refresh logic prevents race conditions
* Auth state machine clearly defined
* Logout clears all local session state
* Revoked sessions invalidate local state
* Offline behavior documented
* Biometric unlock does not replace backend validation
* Redirect interruptions handled

---

# Risks / Rollback

Potential risks:

* insecure token storage
* refresh loops
* stale sessions
* revocation mismatch

Rollback strategy:

1. Clear secure storage
2. Invalidate local auth state
3. Force user re-authentication
4. Disable refresh logic temporarily

---

# Example Request

Platform: Expo Managed
Provider: Supabase
Auth Method: Email/Password
Session Model: JWT
Offline Support: Limited
Biometric Unlock: Optional
Security Level: Standard

---

# Example Response Shape

## Context Summary

Expo managed application using Supabase authentication.

## Session Model

Provider-managed JWT with refresh tokens.

## Storage Policy

Access token in memory.
Refresh token in secure storage.

## Auth State Machine

Unauthenticated → Authenticating → Authenticated → Refreshing → Expired.

## Refresh Strategy

Single-flight refresh.

## Failure Handling

Refresh failure forces logout.

## Next Step

Implement auth context and refresh handler.

# Skill: mobile-auth-session-security

## Purpose

Design, implement, and harden authentication session architecture for **Expo** and **React Native** applications.

This skill guides an AI agent through the full lifecycle of secure mobile authentication including:

- session architecture selection
- token lifecycle design
- secure token storage
- refresh token safety
- session revocation
- biometric re‑authentication boundaries
- provider SDK integration
- failure recovery

The objective is to produce **secure, deterministic, and production‑grade mobile authentication behavior** that remains reliable under real mobile conditions such as:

- app termination
- background resume
- network loss
- token expiration
- concurrent refresh requests
- multi‑device sessions

This skill must always prefer **security, determinism, and maintainability over convenience**.

---

# When to Use

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

# When Not to Use

Do not use this skill when:

- authentication occurs entirely in a browser
- the task only concerns UI login screens
- backend identity architecture is being designed without mobile responsibilities
- the mobile app does not manage authentication state
- the task concerns purely backend identity services

---

# Required Inputs

Before executing this skill the agent must collect the following inputs.

## Platform

- Expo Managed
- Expo + EAS
- Bare React Native

## Target Platforms

- iOS
- Android
- Both

## Authentication Provider

- Firebase
- Supabase
- Auth0
- Clerk
- Custom Backend
- Other OAuth provider

## Authentication Method

- Email/password
- OAuth
- Magic link
- OTP
- SSO

## Backend Session Model

- JWT
- Opaque session
- Hybrid

## Refresh Strategy

- Rotating refresh tokens
- Non‑rotating refresh tokens
- Provider-managed refresh

## Security Requirements

- Standard
- High‑risk
- Regulated

## Offline Support

- None
- Limited
- Required

## Biometric Unlock

- Required
- Optional
- Not used

## Deep Link Redirect

- Required
- Not required

If these inputs are incomplete the agent must explicitly declare assumptions before proceeding.

---

# Framework‑Specific Directives

## Expo Managed

Recommended libraries:

- `expo-secure-store` → secure persistence for refresh tokens
- `expo-auth-session` → OAuth and redirect authentication

Rules:

- Never store authentication tokens in `AsyncStorage`
- Use secure redirect flows for OAuth providers
- Avoid native modules incompatible with Expo runtime

Example installation:

```
npx expo install expo-secure-store expo-auth-session
```

---

## Expo + EAS

Native modules may be used when supported by the EAS build pipeline.

Secure storage options:

- `expo-secure-store`
- `react-native-keychain`

---

## Bare React Native

Preferred secure storage libraries:

- `react-native-keychain`
- native iOS Keychain / Android Keystore

Installation example:

```
npm install react-native-keychain
```

Avoid:

- `AsyncStorage` for tokens
- plaintext persistence

---

# Provider‑Specific Directives

## Supabase

- Use Supabase auth state listeners
- Respect Supabase refresh lifecycle
- Do not implement parallel refresh logic

## Firebase

- Use Firebase Auth state observers
- Avoid manual token refresh if Firebase SDK handles it

## Auth0

- Follow Auth0 session expiration rules
- Align logout behavior with provider session clearing

## Custom Backend

Agent must explicitly define:

- token issuance
- expiration policy
- refresh rotation
- revocation logic
- replay protection

Custom backends **must implement refresh race protection**.

---

# Technical Implementation Patterns

## Auth Context Pattern

Applications should centralize authentication state using a global provider.

Example structure:

```
AuthProvider
 ├── login()
 ├── logout()
 ├── refreshSession()
 ├── getAccessToken()
 └── authState
```

Responsibilities:

- hold authentication state
- manage refresh logic
- protect against refresh race conditions

---

## Refresh Guard

Refresh requests must use **single‑flight protection**.

Example rule:

- if refresh already in progress
- await the existing refresh request

Never allow concurrent refresh attempts.

---

## Session Storage Rules

Access Token:

- stored in memory

Refresh Token:

- stored in secure storage

Never expose refresh tokens to UI components.

---

# Anti‑Patterns

The following patterns are strictly prohibited:

- storing tokens in `AsyncStorage`
- refreshing tokens in multiple parallel requests
- using token presence as the only auth state signal
- ignoring backend revocation events
- exposing refresh tokens to UI code

---

# Decision Tree

## Platform

If **Expo Managed** → use Expo secure storage and auth-session flows.

If **Bare React Native** → use native keychain storage.

---

## Auth Provider

If **provider-based auth** → align lifecycle with provider SDK.

If **custom backend** → implement explicit token lifecycle.

---

## Security Level

If **high‑risk** → require:

- shorter token lifetime
- biometric unlock for sensitive actions
- aggressive revocation handling

---

## Offline Behavior

If offline support required:

- allow cached session within TTL
- refresh session once network returns

---

# Execution Workflow

1. Collect required inputs
2. Detect platform constraints
3. Identify authentication provider
4. Define threat model
5. Choose session architecture
6. Define token lifecycle
7. Define secure storage boundaries
8. Implement auth state machine
9. Implement refresh guard
10. Implement logout behavior
11. Define offline behavior
12. Implement biometric unlock boundary
13. Define failure recovery
14. Validate session integrity
15. Produce structured result

---

# Edge Cases

The skill must handle the following scenarios:

- app killed during OAuth redirect
- app terminated during refresh
- multiple refresh requests
- revoked session on another device
- token expires while offline
- network loss during refresh
- device clock skew
- corrupted secure storage
- deep link redirect interruption
- refresh token rotation mismatch
- biometric settings changed
- app reinstall with active backend session
- logout during refresh

Each case must produce a deterministic recovery path.

---

# Observability

Authentication systems must include diagnostic signals.

Recommended events:

- login success
- login failure
- refresh attempt
- refresh failure
- logout
- session revoked

These events help debug production auth failures.

---

# Output Contract

The agent must produce output containing:

1. Context Summary
2. Assumptions
3. Chosen Session Model
4. Platform Constraints
5. Threat Model
6. Token Lifecycle Plan
7. Storage Policy
8. Auth State Machine
9. Refresh Strategy
10. Offline Behavior
11. Biometric Re‑authentication Policy
12. Failure Handling Strategy
13. Verification Checklist
14. Risks and Rollback
15. Next Implementation Step

---

# Verification Checklist

- secure storage used for tokens
- refresh guard implemented
- auth state machine defined
- logout clears local session state
- revoked sessions invalidate local state
- offline behavior defined
- biometric unlock separated from backend session validity
- redirect interruptions handled

---

# Risks / Rollback

Potential risks:

- insecure token storage
- refresh loops
- stale sessions
- revocation mismatch

Rollback strategy:

1. clear secure storage
2. reset auth state
3. force user re‑authentication
4. disable refresh logic if unstable

---

# Example Request

Platform: Expo Managed
Provider: Supabase
Auth Method: Email/Password
Session Model: JWT
Offline Support: Limited
Biometric Unlock: Optional
Security Level: Standard

---

# Example Response Shape

## Context Summary

Expo managed application using Supabase authentication.

## Session Model

Provider-managed JWT with refresh tokens.

## Storage Policy

Access token stored in memory.
Refresh token stored in secure storage.

## Auth State Machine

Unauthenticated → Authenticating → Authenticated → Refreshing → Expired.

## Refresh Strategy

Single‑flight refresh guard.

## Failure Handling

Refresh failure forces logout.

## Next Step

Implement AuthProvider and refresh guard.