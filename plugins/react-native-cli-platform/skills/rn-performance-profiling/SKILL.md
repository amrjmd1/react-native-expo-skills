---
name: rn-performance-profiling
description: Execution-grade skill for diagnosing React Native CLI performance issues using deterministic profiling and root-cause isolation.
metadata:
  domain: react-native-cli-platform
---

# Skill: rn-performance-profiling

## Purpose
Identify measurable performance bottlenecks in RN apps and deliver verified, low-risk optimizations with before/after evidence.

## When to Use
- Investigating frame drops, startup latency, memory pressure, or slow renders.
- Profiling JS/native thread contention and bridge overhead.
- Validating optimization impact before rollout.

## When Not to Use
- Feature implementation with no profiling requirement.
- Performance claims without measurable baseline collection.

## Required Inputs
- Target platform: `ios` | `android` | `both`.
- React Native version.
- New Architecture enabled or disabled.
- Hermes enabled or disabled.
- App state where issue occurs (cold start, navigation, list scroll, animation, background/foreground).
- Reproduction steps and frequency.
- Device type: simulator/emulator vs physical device.
- Environment: debug vs release build (release required for final conclusions).
- Expected performance behavior (target FPS/startup/memory budget).
- Observed symptom (stutter, startup delay, memory growth, ANR/jank, crash under load).
- Regression context (when introduced, suspect changes, dependency upgrades).

## Framework-Specific Directives
- React Native CLI default workflow:
  - Always profile and validate in release builds for decision-grade results.
  - Do not rely on debug JS mode metrics for optimization decisions.
- Hermes-based profiling:
  - Use Hermes-compatible traces for JS execution hotspots and startup script cost.
  - Compare Hermes/JSC behavior only when runtime engine differs across environments.
- New Architecture considerations:
  - Separate bridge-era assumptions from JSI/TurboModule/Fabric execution paths.
  - Validate performance changes against current architecture mode explicitly.
- RN CLI iOS path:
  - Run iOS-native + RN-compatible profiling and keep measurement conditions stable.
- RN CLI Android path:
  - Run Android-native + RN-compatible profiling and keep measurement conditions stable.
- Cross-platform rule:
  - Isolate JS-thread vs UI-thread bottlenecks before optimization and validate parity on both platforms when scope is `both`.

## Technical Implementation Patterns
- Performance symptom classification:
  - Classify as startup, runtime interaction, scroll/list, animation, memory, or mixed.
- Reproducible profiling session setup:
  - Pin app build type, device class, network state, and test scenario before capture.
- JS thread vs UI thread isolation:
  - Use thread-specific traces/frame timing to identify the blocking side first.
- Frame drop detection workflow:
  - Capture frame metrics during targeted user flow and map drops to trace spans.
- List virtualization profiling:
  - Validate render windowing, item cost, and memory growth under long-scroll scenarios.
- Animation performance inspection:
  - Determine whether stutter is JS-driven work or UI-thread rendering pressure.
- Bridge/JSI overhead diagnosis:
  - Measure high-frequency JS-native calls and serialization overhead in hot paths.
- Cold start measurement pattern:
  - Split startup into initialization phases and identify synchronous blockers.
- Regression comparison workflow:
  - Compare current metrics against prior stable baseline under matched conditions.
- Optimization validation:
  - Apply minimal fix, re-measure, and accept only if improvement exceeds threshold without new regressions.

## Anti-Patterns
- Optimizing before collecting baseline.
- Profiling only in debug mode and treating it as release evidence.
- Guessing root cause without traces or thread-level evidence.
- Mixing multiple unrelated performance problems in one investigation.
- Prematurely applying memoization/caching without bottleneck proof.
- Ignoring platform-specific behavior differences.
- Applying broad refactors without measured bottleneck ownership.

## Decision Tree
- If no baseline exists:
  - collect baseline first.
- If symptom is UI stutter:
  - classify as runtime path and isolate UI vs JS thread contention.
- If symptom is slow startup:
  - run cold-start phase breakdown before runtime optimizations.
- If JS thread is blocked:
  - optimize JS execution path and reduce main-thread-bound JS work.
- If UI thread is blocked:
  - optimize layout/render/animation path on UI side.
- If issue is list rendering:
  - profile virtualization and item render cost before generic caching changes.
- If issue is animation:
  - inspect animation driver path and competing JS work.
- If issue is memory pressure:
  - profile allocation/retention and identify leak or oversized caches.
- If issue is CPU-bound:
  - isolate expensive computation and scheduling hotspots.
- If issue is platform-specific:
  - run platform-only optimization/verification path.
- If regression is confirmed:
  - compare to last stable baseline and bisect suspect changes.
- If optimization impact is insignificant:
  - revert and choose next bottleneck target.

## Execution Workflow
1. Collect required inputs.
2. Classify the performance symptom.
3. Reproduce issue in release build environment.
4. Identify thread or subsystem responsible.
5. Run targeted profiling tools for that subsystem.
6. Isolate root cause with trace evidence.
7. Validate improvement hypothesis with minimal change.
8. Verify fix does not introduce regressions.
9. Produce structured output.

## Edge Cases
- Performance issue appears only on physical devices.
- Android performance behavior diverges from iOS for same flow.
- Issue appears only in release builds, not debug.
- Hermes and JSC show different bottleneck characteristics.
- List virtualization failure causes memory pressure escalation.
- Animation stutter is caused by JS thread blocking.
- Slow startup is caused by synchronous initialization chain.
- Regression appears after dependency upgrade.
- Fix improves latency but increases memory pressure.
- Bridge/JSI optimization changes behavior under load.

## Observability
- Record frame drop metrics and UI smoothness indicators per scenario.
- Track JS thread frame times and long-task frequency.
- Capture memory usage patterns across profiling session phases.
- Record cold-start timing breakdown (native initialization, JS bundle load, and first screen render).
- Compare baselines across builds/releases to detect regressions.
- Record profiling session context (platform, device type, build mode, architecture flags).
- Detect recurring regression patterns across releases.
- Track optimization rollback events when deltas are negative.
- Do not log sensitive user data.

## Output Contract
- Context Summary
- Assumptions
- Performance Symptom Classification
- Profiling Method Used
- Root Cause Analysis
- Proposed Optimization
- Verification Plan
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Baseline and post-change measurements use comparable release conditions.
- Symptom classification matches trace/metric evidence.
- Root cause is isolated to thread/subsystem with supporting data.
- Proposed optimization impact is measurable and above acceptance threshold.
- Cross-platform regressions are checked when scope is `both`.
- No new startup/memory/animation regressions are introduced.

## Risks / Rollback
- Risk: optimization introduces regressions outside measured path.
  - Rollback: revert scoped changes and restore last stable behavior.
- Risk: metric gains are not reproducible.
  - Rollback: discard optimization and reprofile with controlled setup.
