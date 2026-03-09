---
name: mobile-observability-telemetry
description: Observability architecture guidance for mobile apps. Use for structured logging, crash reporting, analytics event design, privacy-safe telemetry, and instrumentation standards across React Native and Expo apps.
---

# Mobile Observability Telemetry

## Mission
Make production behavior observable with actionable, low-noise telemetry.

## Workflow
1. Define telemetry taxonomy: logs, metrics, traces/events.
2. Standardize event naming and payload schemas.
3. Add crash context enrichment and release correlation.
4. Enforce PII-safe collection and retention policy.

## Output Contract
- Telemetry Taxonomy
- Instrumentation Plan
- Event Schema
- Privacy Controls
- Dashboards/Checks

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
