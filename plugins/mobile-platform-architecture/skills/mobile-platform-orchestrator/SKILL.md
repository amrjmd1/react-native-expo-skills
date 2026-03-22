---
name: mobile-platform-orchestrator
description: Execution-grade meta-skill for orchestrating mobile architecture decisions by selecting, sequencing, and coordinating domain-specific skills into a consistent, production-ready system plan.
metadata:
  domain: mobile-platform-architecture
---

# Skill: mobile-platform-orchestrator

## Purpose

Orchestrate domain skills into one coherent mobile system plan by selecting the right skills, sequencing execution, resolving cross-domain conflicts, and enforcing shared assumptions.

## When to Use

- Starting a new React Native / Expo architecture plan.
- Coordinating multi-domain work across data, security, UX, observability, delivery, and monetization.
- Translating product requirements into domain-specific skill execution order.
- Resolving conflicting outputs from multiple domain skills.
- Producing one unified architecture plan from fragmented domain decisions.

## When Not to Use

- Implementing a single-domain task directly.
- Replacing domain skills with generic orchestrator output.
- Writing low-level code, schema, or API logic inside this skill.

## Required Inputs

- `project_type`: `mvp` | `production` | `enterprise`
- `platform`: `expo` | `react-native-cli` | `hybrid`
- `target_platforms`: `ios` | `android` | `both`
- `app_complexity`: `low` | `medium` | `high`
- required domains:
  - identity/security
  - networking/data
  - observability
  - monetization
  - UX
  - performance
  - delivery
- backend availability and ownership model
- offline requirements
- real-time requirements
- monetization requirements
- reliability requirements
- team size and experience level
- priority weighting: time-to-market vs long-term scalability

## Framework-Specific Directives

- Do not solve domain problems directly; delegate to domain skills.
- Always map requirements to domain skills before planning implementation.
- Keep strict separation of concerns between domain outputs.
- Enforce consistency across domain assumptions and lifecycle rules.
- Resolve assumption conflicts before producing final architecture summary.
- Operate as decision + coordination layer only.
- Prefer minimal viable architecture for low complexity.
- Expand to full architecture coverage for high complexity.
- In Expo projects, prioritize Expo-compatible skills first.
- In RN CLI projects, prioritize RN CLI platform and native ownership skills.
- In hybrid projects, explicitly split Expo-managed vs native-owned responsibilities.

## Provider-Specific Directives

- If backend is unavailable:
  - Exclude backend-dependent domain strategies from default selection.
  - Select client-safe fallback skills only when requirements demand it.
- If backend is available:
  - Prefer server-authoritative patterns for auth, data integrity, and monetization.
- If third-party vendor coupling exists (payments, observability, auth):
  - Require vendor-specific domain skill selection and compatibility checks.

## Technical Implementation Patterns

- Domain classification:
  - Map requirements -> domains -> candidate skills.
  - Mark domains as required, optional, or out-of-scope.
- Skill selection:
  - Select the minimal skill set covering required domains.
  - Reject over-selection with no requirement linkage.
- Execution sequencing:
  - Apply default order: `platform -> architecture -> data -> UX -> observability -> delivery`.
  - Move domain earlier only when it is a hard prerequisite.
- Dependency mapping:
  - Identify prerequisite relationships between selected skills.
  - Block downstream skill execution until prerequisites are resolved.
- Conflict resolution:
  - Resolve `state-management vs data-fetching` ownership boundaries.
  - Resolve `UX fidelity vs performance budgets`.
  - Resolve `observability depth vs privacy constraints`.
  - Resolve `payments authority vs offline behavior`.
- Consistency enforcement:
  - Enforce shared assumptions for auth model, data flow, lifecycle states, and environment boundaries.
  - Normalize terminology and contracts across domain outputs.
  - Enforce contract alignment for auth identity model, data ownership boundaries, lifecycle events, and error handling boundaries.
  - Reject conflicting contract assumptions across domains.
- Output aggregation:
  - Merge domain outputs into one system-level plan with explicit dependency references.
  - Surface unresolved conflicts as blocking items.
- Determinism:
  - Same input set must produce same selected domains, skills, execution order, and dependency map.
  - Avoid subjective selection rules not tied to explicit inputs.
- Orchestration determinism:
  - Identical inputs must produce identical domain selection, selected skills, execution order, and dependency map.
  - Avoid subjective or heuristic-only selection not tied to explicit inputs.
- Over-orchestration guard:
  - Do not introduce domains or skills unless explicitly required by inputs.
  - Default to minimal viable system for MVP contexts.
  - Avoid optional domains without direct requirement linkage.

## Anti-Patterns

- Solving domain implementation inside the orchestrator.
- Selecting all available skills regardless of requirements.
- Ignoring dependency ordering between domain skills.
- Allowing conflicting assumptions across domain outputs.
- Treating low-complexity MVP as enterprise architecture by default.
- Treating enterprise requirements as MVP shortcuts.
- Producing fragmented, unmerged domain outputs.
- Mixing system orchestration with low-level implementation details.

## Decision Tree

- If `project_type == mvp` and `app_complexity == low`:
  - Select minimal required domains only.
  - Skip optional domains unless explicit requirement exists.
- If `project_type == production` or `app_complexity == medium`:
  - Include core domains: data, security, UX, observability, delivery.
  - Include monetization/offline/realtime only when required.
- If `project_type == enterprise` or `app_complexity == high`:
  - Include all critical domains and explicit dependency map.
  - Require conflict resolution and rollout-safe delivery plan.
- If offline requirement exists:
  - Include offline-capable data/reliability skills.
- If real-time requirement exists:
  - Include realtime and consistency-related data skills.
- If monetization requirement exists:
  - Include payments/subscriptions skill and revenue observability alignment.
- If platform is `expo`:
  - Prefer Expo-platform domain skills.
- If platform is `react-native-cli`:
  - Prefer RN CLI platform skills.
- If platform is `hybrid`:
  - Split skill routing by ownership boundary.
- If optional domain has no requirement linkage:
  - Exclude it from selected set.

## Execution Workflow

1. Collect inputs.
2. Classify project type and complexity.
3. Map requirements to domains.
4. Select required skills.
5. Define execution order.
6. Define dependencies between skills.
7. Resolve cross-domain conflicts.
8. Enforce consistency rules.
9. Aggregate and normalize outputs across domains.
10. Produce unified system plan.

## Edge Cases

- Over-selection includes unnecessary skills.
- Missing critical domain for required capability.
- Conflicting assumptions between domain outputs.
- MVP is incorrectly orchestrated as enterprise.
- Enterprise scope is incorrectly reduced to MVP path.
- Circular dependencies appear between selected skills.
- Domain outputs contradict each other after sequencing.
- Selected skills produce incompatible contracts despite correct domain selection.
- Incomplete inputs produce invalid orchestration decisions.
- Expo and RN CLI assumptions are mixed incorrectly.

## Observability

- Track selected skills vs required domains coverage.
- Track missing critical domain detections.
- Track conflicting assumption detections.
- Track complexity classification vs selected architecture depth.
- Track over-engineering and under-engineering flags.
- Track unresolved dependency/conflict count.
- Track orchestration correctness outcomes during verification.

## Output Contract

- Context Summary
- Assumptions
- Selected Domains & Skills
- Domain Coverage Summary
- Execution Order
- Dependency Map
- Conflict Resolution Decisions
- System Architecture Summary
- Risks / Trade-offs
- Next Implementation Steps

## Verification Checklist

- Correct skills are selected for required domains.
- No critical domains are missing.
- No unnecessary domains are included.
- Execution order is valid and dependency-safe.
- Dependencies are satisfied before dependent skills run.
- Cross-domain conflicts are resolved or explicitly blocked.
- Final output is cohesive and internally consistent.
- Architecture depth matches project complexity.

## Risks / Rollback

- Risk: over-engineered system slows delivery.
  - Rollback: reduce to minimal required skill set and defer optional domains.
- Risk: missing critical domain creates system gaps.
  - Rollback: re-run classification and enforce required domain coverage gate.
- Risk: conflicting decisions across domains.
  - Rollback: enforce assumption normalization and re-sequence conflicting skills.
- Risk: wrong skill selection path.
  - Rollback: trace requirement-to-skill mapping and replace mismatched skills.
- Risk: scalability shortfall from under-selection.
  - Rollback: promote architecture depth and include omitted reliability/performance domains.
- Risk: time-to-market impact from oversized scope.
  - Rollback: split plan into phased execution with MVP-first core domain set.

## Example

- Scenario:
  - Mid-size production app with auth + payments + offline sync + analytics.
  - Platform: `expo`, targets: `ios` + `android`, complexity: `medium`.
- Orchestration:
  - Select skills for identity/security, data/offline, payments/subscriptions, observability, delivery.
  - Sequence: platform setup -> architecture boundaries -> data/offline -> monetization -> observability -> delivery gates.
  - Resolve conflicts: payments authority vs offline caching, observability depth vs privacy constraints.
- Result:
  - Unified system plan with one dependency map, one assumption set, and phased implementation steps.
