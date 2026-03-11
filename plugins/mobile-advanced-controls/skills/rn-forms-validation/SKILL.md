---
name: rn-forms-validation
description: Execution-grade skill for typed React Native form and validation architecture with deterministic submit flows and resilient error handling.
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
- Form fields and validation constraints.
- Schema strategy (e.g., Zod) and type requirements.
- Submit endpoint behavior and error model.
- UX requirements for validation and network errors.
- Persistence/draft behavior requirements.

## Framework-Specific Directives
- React Hook Form:
  - Keep field registration and controlled wrappers explicit.
- Schema validation:
  - Keep schema as single validation source and infer TS types.
- Cross-platform UX:
  - Ensure focus, keyboard, and error presentation parity.

## Technical Implementation Patterns
- Use schema-first typed form contracts.
- Separate validation errors from submission/network errors.
- Build reusable field primitives with explicit error props.
- Guard duplicate submissions and racing async flows.

## Anti-Patterns
- Divergent UI constraints from schema constraints.
- Mixing validation and transport error states.
- Repeated submit attempts without request-state gating.

## Decision Tree
- If form has complex constraints:
  - use schema-driven typed validation.
- If submit fails with validation errors:
  - focus first invalid field and show actionable messages.
- If submit fails with network/server errors:
  - preserve form state and surface retry path.

## Execution Workflow
1. Collect required inputs.
2. Define schema and inferred type model.
3. Implement reusable typed field components.
4. Implement submit/error handling flow.
5. Add draft persistence behavior if required.
6. Validate UX/accessibility and edge states.
7. Produce structured output.

## Edge Cases
- Async validation race causes stale error state.
- Form state lost after navigation transition.
- Network error incorrectly shown as field validation error.
- Keyboard/focus flow fails on one platform.

## Observability
- Track submit success/failure by error class.
- Track validation error frequency by field.
- Track duplicate-submit prevention events.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Schema and UI constraints are aligned.
- Field components expose typed, reusable contracts.
- Submit flow handles validation and network errors separately.
- Double-submit paths are blocked.

## Risks / Rollback
- Risk: schema mismatch breaks submit path.
  - Rollback: revert to last stable schema and mapped field contracts.
- Risk: new form handling introduces UX regressions.
  - Rollback: restore prior submit/error flow and patch incrementally.
