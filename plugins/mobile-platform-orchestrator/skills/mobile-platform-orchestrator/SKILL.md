---
name: mobile-platform-orchestrator
description: Execution-grade orchestrator for React Native and Expo engineering. Use when requests span multiple domains and require deterministic routing, sequencing, and conflict resolution across platform, security, data, UX, observability, revenue, and delivery workflows.
metadata:
  domain: mobile-platform-orchestrator
---

# Skill: mobile-platform-orchestrator

## Purpose

Orchestrate multi-domain mobile engineering tasks by selecting the correct specialist domains, sequencing execution safely, resolving cross-domain conflicts, and returning one cohesive implementation plan.

## When to Use

- The request spans multiple domains (for example auth + data + release).
- The developer is unsure which specialist skill to invoke first.
- You need a phased execution plan with dependency ordering.
- You need one final architecture/implementation plan merged from several domains.

## When Not to Use

- The request is single-domain and can be solved directly by a specialist skill.
- The task is purely UI wording or copywriting.
- The task requires deep implementation in one domain only.

## Required Inputs

- `platform`: `expo` | `react-native-cli` | `hybrid`
- `project_stage`: `mvp` | `production` | `enterprise`
- `target_platforms`: `ios` | `android` | `both`
- `required_domains`: security, data, ux, observability, revenue, delivery, architecture
- `constraints`: timeline, compliance, performance, team capacity
- `risk_tolerance`: low | medium | high

If any input is missing, declare assumptions explicitly before routing.

## Framework-Specific Directives

- For `expo`, route platform and release concerns first through Expo-focused skills.
- For `react-native-cli`, prioritize native-boundary and build-signing concerns early.
- For `hybrid`, split decisions by ownership boundary (managed vs native-owned areas).
- Keep orchestration as a coordination layer; do not replace specialist implementation details.

## Provider-Specific Directives

- If auth provider is present (`auth0`, `firebase`, `supabase`, `custom`), route identity tasks to security skills before data flow finalization.
- If payment provider is present (`apple`, `google`, `stripe`, `revenuecat`), route entitlement and billing logic before release sign-off.
- If observability provider is present (`sentry`, `datadog`, `newrelic`, `custom`), route telemetry standards before incident policy design.
- If provider is custom/unknown, require explicit contract and failure semantics before final plan approval.

## Technical Implementation Patterns

- Build a domain map: `requirement -> domain -> skill`.
- Mark each domain as `required`, `optional`, or `out-of-scope`.
- Sequence with prerequisites first: platform -> security/data -> UX -> observability -> revenue -> delivery.
- Merge outputs into one normalized plan with shared assumptions.
- Block finalization when assumptions conflict across domains.

## Anti-Patterns

- Selecting all skills without requirement mapping.
- Mixing low-level implementation with orchestration decisions.
- Ignoring dependency order between domain outputs.
- Producing separate, unmerged plans per domain.

## Decision Tree

- If scope is single-domain, route directly to the specialist skill.
- If scope is multi-domain with low risk, use minimal required domains only.
- If scope is multi-domain with high risk, enforce full dependency mapping and conflict checks.
- If platform is unknown, gather platform input before domain routing.

## Execution Workflow

1. Collect required inputs and constraints.
2. Classify request scope and risk.
3. Map requirements to domains and candidate skills.
4. Select minimal sufficient skill set.
5. Define dependency-safe execution order.
6. Resolve cross-domain conflicts and assumptions.
7. Produce one consolidated implementation plan.
8. Attach verification and rollback checkpoints.

## Edge Cases

- Incomplete input causes wrong domain routing.
- Conflicting outputs between security and UX flows.
- Release constraints conflict with architecture changes.
- Offline requirements conflict with revenue or auth assumptions.

## Observability

- Record selected domains and rejected domains with reason.
- Record unresolved conflicts as blocking items.
- Track assumption count and risk level in final output.
- Never include secrets in orchestration logs.

## Output Contract

Return sections in this order:
1. Context Summary
2. Assumptions
3. Domain Routing Decision
4. Execution Order
5. Consolidated Implementation Plan
6. Verification Checkpoints
7. Risks / Rollback
8. Next Implementation Step

## Verification Checklist

- Domains selected match stated requirements.
- No required domain is missing.
- Dependency order is valid.
- Cross-domain conflicts are resolved or blocked explicitly.
- Final plan is deterministic and actionable.

## Risks / Rollback

- Risk: over-orchestration adds unnecessary scope.
  - Rollback: reduce to minimal required domains.
- Risk: wrong routing due to missing inputs.
  - Rollback: re-run classification after collecting missing inputs.
- Risk: conflicting domain outputs block delivery.
  - Rollback: escalate conflict, lock final decisions, and re-sequence.

## Example Request

"We are shipping a React Native app with Auth0, offline sync, and subscriptions. Provide a phased plan for architecture, security hardening, telemetry, and release readiness."

## Example Response Shape

- Context Summary: platform, stage, constraints.
- Assumptions: explicit unknowns.
- Domain Routing Decision: selected domains + reason.
- Execution Order: phased sequence with dependencies.
- Consolidated Implementation Plan: per phase actions.
- Verification Checkpoints: acceptance gates.
- Risks / Rollback: top risks and fallback actions.
- Next Implementation Step: first concrete task.
