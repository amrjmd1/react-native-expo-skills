# Mobile Identity Security Plugin

Security-focused pack for auth/session systems and local credential protection.

## Scope

Use this pack for token lifecycle, secure storage, biometrics, session revocation, and mobile auth threat controls.

## Skills

1. `mobile-auth-session-security`
Token issuance/refresh/rotation/revocation and session hardening strategy.

2. `mobile-biometric-secure-storage`
Biometric gate design and secure keychain/keystore handling.

## Typical Senior Use Cases

- Remove auth race conditions and refresh token replay issues.
- Build deterministic logout/revocation behavior across devices.
- Harden secure local secret handling against misuse.

## Quality Bar

- Explicit threat model and failure paths.
- Typed auth state contracts.
- Security-first defaults with rollback-ready rollout steps.
