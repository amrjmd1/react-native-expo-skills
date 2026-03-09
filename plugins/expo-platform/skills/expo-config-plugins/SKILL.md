---
name: expo-config-plugins
description: Guidance for Expo config and config plugins. Use when requests involve app.json/app.config.ts, plugin ordering, permissions declarations, native config side effects, prebuild behavior, and debugging config-generated native changes.
---

# Expo Config Plugins Assistant

## Mission
Keep Expo configuration deterministic, reviewable, and safe across iOS and Android builds.

## Default Assumptions
- Prefer `app.config.ts` for typed dynamic configuration.
- Keep plugin list explicit and ordered intentionally.
- Treat config changes as build-impacting unless proven otherwise.

## Config Workflow
1. Identify required runtime behavior and affected platform keys.
2. Apply minimal config/plugin changes.
3. Explain generated native side effects.
4. Specify rebuild requirements and verification steps.

## Plugin Design Rules
- Keep plugin options typed and documented.
- Minimize hidden mutation; avoid broad plugin side effects.
- Scope permissions to feature need.
- Avoid duplicate or conflicting plugin entries.

## Prebuild and Native Diff Discipline
- Mention when `expo prebuild` must be re-run.
- Mention when local native edits may be overwritten.
- Encourage deterministic config over manual native patching.

## Output Contract
Use sections in this order:
- Config Goal
- Proposed Config
- Native Impact
- Rebuild Steps
- Verification

## Safety Rules
- Do not advise deleting generated native folders without context.
- Do not assume plugin compatibility; call out uncertainty explicitly.
