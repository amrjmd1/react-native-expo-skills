---
name: mobile-biometric-secure-storage
description: Biometric and secure storage guidance for mobile apps. Use for keychain/keystore patterns, biometric prompt flows, fallback auth, and secure local secret handling across iOS/Android.
---

# Biometric and Secure Storage

## Mission
Implement secure, user-friendly local credential protection.

## Workflow
1. Classify what data can be stored locally and in what form.
2. Use platform secure storage primitives with explicit access controls.
3. Add biometric gate with fallback and lockout handling.
4. Provide device change and app reinstall recovery behavior.

## Output Contract
- Storage Policy
- Biometric Flow
- Typed Wrapper API
- Edge Cases
- Verification Steps

## Senior Execution Mode
- Start by identifying system boundaries, assumptions, and risk level.
- Prefer smallest safe change that can be validated quickly.
- Keep recommendations production-focused: reliability, maintainability, and operational clarity.
- Make platform differences explicit when behavior diverges between iOS and Android.

## Decision Heuristics
- Prefer deterministic and testable architectures over clever shortcuts.
- Choose explicit typed contracts for all module boundaries.
- Reject ambiguous state ownership; define single source of truth.
- Prioritize debuggability and rollback safety for release-impacting changes.

## Code Quality Gates
- Enforce strict TypeScript (no implicit any, typed inputs/outputs).
- Avoid hidden side effects and broad mutable shared state.
- Keep components/services single-purpose and composable.
- Prevent unnecessary re-renders by controlling subscription and prop surfaces.

## Review Checklist
- Correctness: Does the solution handle edge and failure states?
- Scale: Does it remain maintainable as features grow?
- Performance: Are hot paths optimized with measurable intent?
- Operations: Can this be monitored, debugged, and rolled back safely?

## Response Style
Always provide:
- clear problem framing,
- actionable implementation,
- verification steps,
- one senior-level follow-up recommendation.
