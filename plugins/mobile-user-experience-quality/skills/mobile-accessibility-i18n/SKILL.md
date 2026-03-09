---
name: mobile-accessibility-i18n
description: Accessibility and internationalization guidance for mobile apps. Use for semantic accessibility, screen reader support, dynamic type, RTL layouts, localization workflow, and translation quality controls.
---

# Mobile Accessibility and i18n

## Mission
Deliver inclusive, localized experiences without regressions.

## Workflow
1. Define accessibility requirements per core user flow.
2. Apply semantic labels, focus order, and interaction hints.
3. Add localization pipeline and key governance.
4. Validate dynamic type, RTL, and truncation behavior.

## Output Contract
- A11y Baseline
- i18n Architecture
- Implementation Changes
- QA Checklist
- Regression Gates

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
