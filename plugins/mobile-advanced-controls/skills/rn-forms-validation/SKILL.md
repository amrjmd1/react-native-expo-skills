---
name: rn-forms-validation
description: Execution-grade skill for designing React Native form state, validation boundaries, async/server error handling, and predictable submission architecture.
metadata:
  domain: mobile-advanced-controls
---

# Skill: rn-forms-validation

## Purpose
Design maintainable, typed form systems with consistent validation, submission handling, and denied/error-state UX.

## When to Use
- Building complex forms with React Hook Form and schema validation.
- Standardizing async submit and error handling paths.
- Fixing validation inconsistency or double-submit issues.

## When Not to Use
- Simple uncontrolled inputs with no validation requirements.
- Backend-only validation logic changes.

## Required Inputs
- Form complexity: `simple` | `medium` | `large` | `multi-step`.
- Target platform: `ios` | `android` | `both`.
- Field types involved and cross-field dependencies.
- Sync validation requirements.
- Async validation requirements.
- Server-side validation dependency and error contract.
- Persistence/draft-save requirements.
- Offline submission requirements.
- Submission retry rules.
- Accessibility expectations.
- Localization/i18n requirements.
- Context: `greenfield` | `migration`.

## Framework-Specific Directives
- React Native form architecture:
  - Keep draft form state isolated from committed domain state.
  - Separate field-level validation from submission-level validation.
  - Keep server validation errors distinct from client validation errors.
- React Hook Form + schema tools (e.g., Zod/Yup):
  - Keep schema as validation source-of-truth and infer TS types where possible.
  - Use controlled/uncontrolled strategy intentionally per field type/performance needs.
- Async validation:
  - Do not block UI interaction unnecessarily while async checks are pending.
  - Enforce deterministic stale-response handling for changed field values.
- Cross-platform UX:
  - Ensure focus, keyboard, and error rendering parity.

## Technical Implementation Patterns
- Field vs form ownership map:
  - define field-local state, form aggregate state, and submission state owners.
- Touched/dirty/submitted lifecycle model:
  - make transitions explicit and deterministic for error visibility.
- Sync validation boundary:
  - run structural/format checks client-side before submission.
- Async validation boundary:
  - isolate network-backed checks with cancellation/staleness guards.
- Server error mapping contract:
  - map server field/global errors into stable client error buckets.
- Draft vs committed separation:
  - keep draft edits separate from committed domain entities until success.
- Multi-step checkpoint model:
  - validate per step and on final submit with explicit step ownership.
- Submission state machine:
  - `idle -> validating -> submitting -> success|failure` with retry branch.
- Retry-safe submission pattern:
  - prevent duplicate submissions and preserve user intent on retry.
- Validation message timing rules:
  - show messages after meaningful interaction or submit attempt.

## Anti-Patterns
- Mixing form draft state with committed domain state.
- Validating every field on every keystroke without need.
- Collapsing sync/async/server validation into one generic error bucket.
- Showing errors before meaningful user interaction.
- Blocking submission without deterministic error ownership.
- Duplicating conflicting validation rules across layers.
- Persisting invalid drafts without schema/version awareness.
- Repeated submit attempts without request-state gating.

## Decision Tree
- If form is `simple`:
  - use lightweight field validation with minimal orchestration.
- If form is `multi-step`:
  - use per-step checkpoints plus final submission validation.
- If validation is sync-only:
  - resolve on client before submit transition.
- If async validation is required:
  - run async checks with stale-result guards and non-blocking UI.
- If field is optional:
  - defer validation until touched or submit.
- If field is required:
  - enforce deterministic required check before submission.
- If server is authoritative for rule set:
  - treat server validation as final and map errors predictably.
- If draft-save is required:
  - persist draft locally and separate from committed state.
- If submission is retryable:
  - retain draft + submission context and expose controlled retry path.
- If submission is fail-fast:
  - stop on first blocking error and surface deterministic ownership.

## Execution Workflow
1. Collect required inputs.
2. Classify form complexity and validation needs.
3. Define draft vs committed state ownership.
4. Define field/form/submission validation boundaries.
5. Define error timing and presentation rules.
6. Define submission lifecycle and retry behavior.
7. Define accessibility and localization requirements.
8. Validate performance and maintainability risks.
9. Produce structured output.

## Edge Cases
- Async validation returns after field value changed.
- Server rejects value previously accepted by client validation.
- Multi-step flow loses draft state during navigation.
- User retries after partial submission failure.
- Field error clears visually while submission remains blocked.
- Localized validation message overflows constrained UI.
- Offline queue submits stale draft data.
- Persisted draft schema changes after app update.
- Keyboard/focus flow fails on one platform.

## Observability
- Track validation failure rate by field and form step.
- Observe async validation latency and timeout frequency.
- Detect repeated submission failures by error class.
- Surface abandoned forms after repeated validation errors.
- Detect mismatch between client validation pass and server rejection.
- Observe retry loops or stuck submission states.
- Track duplicate-submit prevention events.
- Do not log sensitive user input values.

## Output Contract
- Context Summary
- Assumptions
- Form State Ownership
- Validation Boundaries
- Submission Lifecycle
- Error Presentation Rules
- Accessibility / Localization Notes
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Draft and committed state ownership is explicit and isolated.
- Sync, async, and server validation boundaries are deterministic.
- Error timing is consistent with touched/dirty/submitted lifecycle.
- Submission lifecycle and retry behavior are explicit.
- Accessibility and localized error rendering are validated.
- Double-submit and stale-async-result paths are blocked.

## Risks / Rollback
- Risk: schema mismatch breaks submit path.
  - Rollback: revert to last stable schema and mapped field contracts.
- Risk: new form handling introduces UX regressions.
  - Rollback: restore prior submit/error flow and patch incrementally.
