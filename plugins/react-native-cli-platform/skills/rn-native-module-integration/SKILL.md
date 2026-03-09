---
name: rn-native-module-integration
description: Integration guidance for native SDKs/modules in React Native CLI projects. Use for autolinking/manual linking issues, Podfile/Gradle updates, permission wiring, initialization order, and bridge/native module compatibility troubleshooting.
---

# RN Native Module Integration

## Mission
Integrate native modules with reproducible setup and low long-term maintenance risk.

## Integration Workflow
1. Validate library compatibility with RN version and architecture.
2. Apply iOS and Android setup in deterministic order.
3. Provide typed JS wrapper around native entry points.
4. Provide rebuild and smoke test steps.

## Setup Discipline
- Keep Podfile and Gradle edits minimal and commented when non-obvious.
- Avoid global Gradle/Pod hacks when library-scoped change is possible.
- Handle permission and plist/manifest declarations together.

## Bridge Safety
- Guard calls when module is unavailable.
- Normalize native error shapes into typed JS errors.
- Provide fallback behavior for unsupported platform/device.

## Output Contract
Use sections in this order:
- Compatibility Check
- iOS Changes
- Android Changes
- JS Typed Wrapper
- Rebuild and Verification
