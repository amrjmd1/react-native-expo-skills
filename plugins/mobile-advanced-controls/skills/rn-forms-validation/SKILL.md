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
