---
name: expo-platform-assistant
description: Execution-grade orchestration skill for Expo platform triage, high-level guidance, and routing into specialized Expo skills.
metadata:
  domain: expo-platform
---

# Skill: expo-platform-assistant

## Purpose
Provide deterministic Expo problem triage and cross-skill routing, deciding when to give lightweight high-level guidance versus escalating to specialized Expo skills for deep execution.

## When to Use
- Classifying Expo requests into config, release, OTA, permissions, or mixed domains.
- Determining workflow-aware routing for managed, Expo + EAS, or bare-with-Expo projects.
- Providing fast high-level guidance when deep implementation is not required.
- Coordinating multi-domain handoffs across specialized Expo skills.

## When Not to Use
- Non-Expo projects.
- Pure backend architecture tasks with no mobile platform impact.
- Replacing detailed implementation logic already owned by specialized Expo skills.

## Required Inputs
- Workflow mode: `Expo Managed` | `Expo + EAS` | `Bare React Native with Expo integration`.
- Request type: architecture triage | quick guidance | implementation planning | release issue | config issue | OTA issue | permission issue.
- Target platform: `ios` | `android` | `both`.
- App maturity: prototype | production | maintenance.
- Urgency: exploratory | active bug | release-blocking.
- Affected domain: config | release | OTA | permissions | mixed.
- Whether specialized deep-skill output is required.
- Whether user expects fast high-level guidance or detailed execution design.
- Current blocking symptom and expected outcome.

## Framework-Specific Directives
- Expo Managed:
  - Prefer high-level guidance first; route domain-specific deep work to specialized skills.
  - Avoid native/bare recommendations unless managed constraints are explicitly hit.
- Expo + EAS:
  - Treat build/release/OTA routing as profile-aware and environment-specific.
  - Escalate release and OTA concerns early to avoid shallow misclassification.
- Bare React Native with partial Expo usage:
  - Confirm Expo-owned vs native-owned boundary before routing.
  - Route only Expo-owned concerns to Expo specialized skills; flag native-owned gaps explicitly.

## Technical Implementation Patterns
- Problem triage matrix:
  - Classify by domain (`config`, `release`, `OTA`, `permissions`, `mixed`) and urgency.
- Skill routing table:
  - `config` -> `expo-config-plugins`
  - `release` -> `expo-eas-release`
  - `OTA` -> `expo-updates-ota`
  - `permissions` -> `expo-device-permissions`
- Lightweight vs deep mode:
  - Use lightweight guidance only for non-blocking/high-level requests.
  - Use deep-mode routing for active bugs, release risks, and implementation requests.
- Mixed-domain escalation path:
  - Split into ordered sub-problems and route sequentially.
  - Safe dependency order examples:
    - `config -> release` (resolve configuration determinism before EAS build/release analysis).
    - `release -> OTA` (verify binary release stability before OTA routing decisions).
    - `permissions -> release` (verify store compliance before final release workflows).
    - `config -> OTA` (validate runtimeVersion/channel rules before OTA publishing).
- Cross-skill handoff contract:
  - Pass workflow mode, platform, urgency, assumptions, and blocking symptom to each routed skill.
  - Required handoff payload for `expo-config-plugins`, `expo-eas-release`, `expo-updates-ota`, `expo-device-permissions`:
    - `workflow_mode`, `target_platform`, `urgency_level`, `problem_classification`.
    - `blocking_symptom`, `known_constraints`, `triage_assumptions`.
    - `expected_output_depth` (`high-level` or `execution design`).
- Ambiguity handling:
  - Make bounded assumptions explicitly; escalate instead of guessing when risk is high.
- Orchestration summary format:
  - Return concise classification, routing decision, handoff order, and escalation notes.

## Anti-Patterns
- Duplicating detailed logic owned by specialized skills.
- Routing too late after giving incorrect shallow advice.
- Collapsing multi-domain problems into one vague response.
- Mixing config, release, OTA, and permissions without separation.
- Giving build/release advice before checking workflow mode and urgency.
- Escalating everything when lightweight guidance is sufficient.

## Decision Tree
- If request is quick/high-level and non-blocking:
  - provide lightweight guidance and stop.
- If request needs implementation detail or is release-blocking:
  - route to specialized skill(s).
- If single-domain `config` issue:
  - route to `expo-config-plugins`.
- If single-domain EAS build/release issue:
  - route to `expo-eas-release`.
- If single-domain OTA/channel/runtime issue:
  - route to `expo-updates-ota`.
- If single-domain permission declaration/runtime UX issue:
  - route to `expo-device-permissions`.
- If mixed or unclear problem:
  - provide orchestration summary, then route in dependency order.
- If workflow mode is unclear:
  - assume lowest-risk mode, state assumption, and route conservatively.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Identify workflow mode and platform scope.
3. Classify request into Expo domain(s).
4. Decide whether lightweight guidance is sufficient.
5. Route to one or more specialized skills if deep execution is needed.
6. Define handoff order for multi-domain cases.
7. Produce concise orchestration output.

## Edge Cases
- Vague Expo request actually spans config + OTA.
- Build failure root cause is config plugin ordering.
- Permission issue is actually store/release compliance risk.
- OTA failure is runtimeVersion/build-profile mismatch.
- User asks for quick help but issue is release-blocking and needs escalation.
- Mixed-domain issue where wrong routing would produce unsafe advice.

## Observability
- Log detected workflow mode and platform scope.
- Log routed domain(s) and selected specialized skills.
- Log whether response stayed lightweight or escalated to deep routing.
- Track ambiguous routing cases for future triage refinement.
- Never log secrets.

## Output Contract
- Context Summary
- Workflow Mode
- Problem Classification
- Routing Decision
- Lightweight Guidance (if sufficient)
- Specialized Skill Handoff
- Risks / Escalation Notes
- Next Implementation Step

## Verification Checklist
- Workflow mode is identified before recommendations.
- Domain classification is explicit (single vs mixed).
- Routing decision maps to correct specialized skill(s).
- Lightweight mode is used only when risk is low.
- Multi-domain handoff order is explicit and safe.
- Escalation notes capture risks when assumptions are used.

## Risks / Rollback
- Risk: wrong domain routing leads to incorrect advice.
  - Rollback: reclassify with explicit triage matrix and reroute to correct specialized skill.
- Risk: shallow guidance given for release-blocking issue.
  - Rollback: escalate immediately to deep skill routing with urgency flag.
