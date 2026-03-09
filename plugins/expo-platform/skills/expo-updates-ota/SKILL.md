---
name: expo-updates-ota
description: Execution-grade skill for Expo OTA update strategy, compatibility controls, rollout safety, and incident rollback handling.
metadata:
  domain: expo-platform
---

# Skill: expo-updates-ota

## Purpose
Define safe Expo OTA release operations with strict runtime compatibility, controlled rollout, and deterministic rollback behavior.

## When to Use
- Designing channel/branch OTA strategy.
- Publishing/promoting OTA updates across environments.
- Hardening compatibility policy between binaries and updates.
- Troubleshooting updates that fail to load/apply.

## When Not to Use
- Binary release pipeline tasks without OTA scope.
- Runtime features unrelated to update distribution.

## Required Inputs
- Runtime version policy and compatibility rules.
- Channel/branch topology by environment.
- Target app binary versions in the field.
- Rollout percentages and promotion policy.
- Monitoring thresholds and rollback triggers.
- Incident ownership and response path.

## Framework-Specific Directives
- Expo Managed:
  - Keep updates policy fully config-driven.
  - Enforce runtime compatibility checks before publish.
- Expo + EAS:
  - Align branch/channel strategy with EAS release workflow.
  - Keep env-specific update pipelines isolated.
- Bare React Native:
  - Validate Expo Updates integration and runtime mapping before rollout.
  - Treat native binary compatibility as hard gate.

## Technical Implementation Patterns
- Maintain explicit runtime-version gating rules.
- Use staged rollout then promotion, never full blast by default.
- Separate publish, verify, and promote steps with stop conditions.
- Keep rollback command path pre-defined and tested.
- Capture update group metadata for incident triage.

## Anti-Patterns
- Publishing updates across incompatible runtime boundaries.
- Promoting directly to production without staged verification.
- Mixing staging and production channels.
- Starting incident triage without update group/channel mapping.

## Decision Tree
- If runtime compatibility is uncertain:
  - block publish.
- If staged rollout metrics are healthy:
  - promote incrementally.
- If crash/error threshold breaches after publish:
  - rollback immediately and pause promotion.
- If issue source is channel routing mismatch:
  - fix routing and re-verify before new publish.

## Execution Workflow
1. Collect required inputs and assumptions.
2. Validate runtime compatibility and environment mapping.
3. Define channel/branch rollout design.
4. Publish OTA to target branch.
5. Verify telemetry and runtime health gates.
6. Promote gradually to broader channels.
7. Execute rollback if thresholds fail.
8. Produce structured output.

## Edge Cases
- Binary in field cannot accept latest OTA runtime.
- Incorrect channel mapping exposes update to wrong audience.
- Asset/runtime crash appears only after promotion.
- Rollback command succeeds but clients cache stale update state.

## Observability
- Track OTA adoption rate by runtime version.
- Track update load/apply failures by channel and platform.
- Track rollback trigger frequency and time-to-rollback.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Runtime compatibility gate is enforced.
- Channel/branch mapping is correct for each environment.
- Staged rollout and promotion gates are defined.
- Monitoring thresholds and rollback triggers are explicit.
- Rollback procedure is executable and tested.

## Risks / Rollback
- Risk: incompatible update causes startup/runtime failure.
  - Rollback: revert to last stable update group and pause promotion.
- Risk: channel misrouting affects unintended users.
  - Rollback: restore prior channel mapping and republish corrected target.
