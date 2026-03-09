---
name: mobile-auth-session-security
description: Authentication and security guidance for mobile apps. Use for token lifecycle, refresh/rotation, secure storage boundaries, session hardening, and auth threat modeling in React Native or Expo applications.
---

# Mobile Auth Session Security

## Mission
Design secure, resilient authentication and session flows for mobile apps.

## Workflow
1. Model identity boundaries and trust zones.
2. Define token issue, refresh, rotation, revocation lifecycle.
3. Separate storage of access, refresh, and derived session state.
4. Provide typed auth state machine and failure handling.

## Security Controls
- Minimize token lifetime and scope.
- Implement refresh race protection and replay defenses.
- Handle compromised session invalidation deterministically.
- Enforce logout and device revocation semantics.

## Output Contract
- Threat Model
- Auth Flow
- Typed Implementation
- Failure/Recovery Paths
- Verification Checklist

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
