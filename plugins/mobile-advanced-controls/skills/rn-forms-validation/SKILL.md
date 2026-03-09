---
name: rn-forms-validation
description: Form architecture guidance for React Native with React Hook Form and Zod. Use for typed form schemas, reusable field primitives, async validation, submission error handling, and high-quality form UX.
---

# RN Forms and Validation

## Mission
Build forms that are typed, resilient, and easy to maintain under changing product requirements.

## Form Workflow
1. Define schema-first validation with inferred TypeScript types.
2. Build reusable controlled field components.
3. Implement consistent async submit and error handling flow.
4. Standardize validation messaging and accessibility behavior.

## Implementation Rules
- Keep validation schema and UI constraints aligned.
- Use field-level components with explicit props and error contracts.
- Disable double-submit paths and race conditions.
- Preserve draft state intentionally when navigating away.

## UX Quality Gates
- Show actionable validation messages.
- Focus first invalid input on submit failure.
- Handle network errors separately from validation errors.

## Output Contract
Use sections in this order:
- Schema Design
- Typed Form Model
- Reusable Fields
- Submit/Error Flow
- Test Cases

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
