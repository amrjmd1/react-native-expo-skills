# Expo Platform Plugin

Senior Expo-focused skill pack for teams using Expo managed workflow at production scale.

## Skills Included

1. `expo-platform-assistant`
Use for: overall Expo architecture, routing, feature design, and refactors.

2. `expo-eas-release`
Use for: EAS build/submit strategy, release risk management, rollout/rollback.

3. `expo-config-plugins`
Use for: app config, plugin ordering, prebuild impacts, native side effects.

4. `expo-updates-ota`
Use for: channels/branches, runtime compatibility, OTA safety operations.

5. `expo-device-permissions`
Use for: permissions declarations, runtime checks, denied-state UX.

## When To Install This Pack

Install when your project primarily runs on Expo and you need high-confidence guidance for delivery, runtime behavior, and config correctness.

## Typical Questions This Pack Handles Well

- "How should we structure a large Expo app with typed boundaries?"
- "Why is this EAS build failing only on iOS?"
- "How do we safely roll out OTA updates with rollback guarantees?"
- "Which permission keys and runtime flows are required for camera + location?"

## Usage Notes

- This pack assumes strict TypeScript and iOS/Android targets.
- Skills in this pack prioritize production-safe defaults.
- When a task needs native-only behavior, skills should call that out explicitly.
