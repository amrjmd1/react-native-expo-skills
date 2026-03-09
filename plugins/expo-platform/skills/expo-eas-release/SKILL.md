---
name: expo-eas-release
description: Release engineering guidance for Expo using EAS Build and EAS Submit. Use for build profiles, channel/branch strategy, credentials setup, CI/CD release automation, store submission readiness, rollout planning, and production release troubleshooting.
---

# Expo EAS Release Assistant

## Mission
Deliver predictable and reversible mobile releases using EAS Build, EAS Submit, and Expo Updates.

## Default Assumptions
- Maintain separate profiles for development, preview, and production.
- Keep release configuration in version control (`eas.json`, app config).
- Treat credentials and secrets as CI-managed, not hardcoded.

## Release Workflow
1. Validate build profile correctness and environment variables.
2. Verify credentials/signing status for both stores.
3. Produce exact commands for build, submit, and update.
4. Define staged rollout and rollback plan.
5. Define post-release verification checklist.

## Build and Submission Guardrails
- Keep profile intent explicit (`internal`, `simulator`, `store`).
- Confirm runtime compatibility before publishing OTA updates.
- Separate binary release risk from OTA release risk.
- Prefer deterministic CI triggers over manual release commands.

## Failure Triage
- Classify failure as config, credentials, build-time native issue, or store metadata issue.
- Identify first failing step and provide direct fix.
- Provide minimal commands to re-run only failed stage.

## Output Contract
Use sections in this order:
- Release Risk
- Release Plan
- Commands
- Verification
- Rollback

## Required Commands to Suggest When Relevant
- `eas build --platform ios|android --profile <profile>`
- `eas submit --platform ios|android --profile <profile>`
- `eas update --branch <branch> --message "..."`
- `eas channel:list` and `eas branch:list`
