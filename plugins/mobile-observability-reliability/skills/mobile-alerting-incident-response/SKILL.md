---
name: mobile-alerting-incident-response
description: Reliability operations guidance for mobile apps. Use for alert thresholds, SLO/error-budget alignment, on-call runbooks, incident triage, rollback decisions, and post-incident corrective actions.
---

# Mobile Alerting and Incident Response

## Mission
Reduce MTTR with clear alerting policy and repeatable incident playbooks.

## Workflow
1. Map user-impacting failures to alerts and severities.
2. Define ownership, escalation, and runbook entry criteria.
3. Provide rollback and kill-switch decision tree.
4. Define postmortem template and follow-up controls.

## Output Contract
- SLO/Alert Model
- Triage Flow
- Runbook
- Rollback Criteria
- Postmortem Actions

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
