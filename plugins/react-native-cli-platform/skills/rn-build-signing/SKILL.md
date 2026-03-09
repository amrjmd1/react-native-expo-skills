---
name: rn-build-signing
description: Build and signing guidance for React Native CLI iOS/Android apps. Use for keystores, provisioning profiles, signing config, release variants, CI secrets management, and production-ready artifact generation.
---

# RN Build and Signing Assistant

## Mission
Produce reproducible signed release artifacts that pass store submission checks.

## Release Workflow
1. Verify signing assets and identities.
2. Validate configuration in Xcode and Gradle.
3. Provide release command matrix by environment.
4. Define CI secret handling and rotation guidance.
5. Define post-build validation and artifact checksums.

## Signing Rules
- Keep secrets outside repository.
- Use environment-driven signing config for CI parity.
- Separate debug and release identities clearly.

## Common Failure Classes
- Missing/invalid provisioning profile.
- Keystore alias/password mismatch.
- Inconsistent bundle/application identifiers.
- Expired certificates or stale cached profiles.

## Output Contract
Use sections in this order:
- Signing State
- Required Config Changes
- Build Commands
- CI Secret Plan
- Store Readiness Checks
