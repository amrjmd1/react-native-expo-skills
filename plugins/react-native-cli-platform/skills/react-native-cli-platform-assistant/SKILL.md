---
name: react-native-cli-platform-assistant
description: Execution-grade orchestration skill for React Native CLI platform triage and routing to specialized RN platform skills.
metadata:
  domain: react-native-cli-platform
---

# Skill: react-native-cli-platform-assistant

## Purpose
Triage React Native CLI platform issues and route requests to the correct deep domain path while giving concise, safe high-level guidance when deep implementation is unnecessary.

## When to Use
- Classifying issues across JS, bridge, native build, runtime, performance, and release.
- Selecting minimal-safe next steps for iOS/Android CLI projects.
- Coordinating multi-domain tasks that require ordered handling.

## When Not to Use
- Non-React-Native-CLI projects.
- Domain-deep implementation that should be handled directly by a specialized skill.

## Required Inputs
- Target platform: `ios` | `android` | `both`.
- Request type: architecture guidance | integration issue | release/signing issue | performance issue.
- Workflow mode: RN CLI standard | RN CLI with heavy native ownership.
- Urgency level: exploratory | bug investigation | release-blocking.
- Reproduction context and current blocking symptom.
- Whether user expects quick guidance or full execution design.
- Known constraints (CI path, SDK/toolchain versions, release deadlines).

## Framework-Specific Directives
- React Native CLI (default):
  - Treat native folders as source of truth and validate toolchain parity first.
  - Route deep native integration, signing, and profiling tasks to specialized skills.
- RN CLI with heavy native ownership:
  - Separate RN-owned fixes from platform-owned native changes before routing.
  - Escalate quickly when ownership boundary is unclear and risk is high.
- CI-integrated release workflows:
  - Keep build-path assumptions explicit and CI-aligned before giving release guidance.

## Technical Implementation Patterns
- Problem triage matrix:
  - classify by domain (`integration`, `signing/release`, `performance`, `mixed`) and urgency.
- Domain routing table:
  - native SDK/module integration issues -> `rn-native-module-integration`.
  - signing/keystore/provisioning issues -> `rn-build-signing`.
  - performance regressions/profiling requests -> `rn-performance-profiling`.
- Lightweight guidance vs deep routing mode:
  - use lightweight mode for low-risk directional questions only.
  - escalate to specialized skill for implementation, regressions, or release risk.
- Mixed-domain escalation:
  - split incident into ordered sub-problems and route sequentially.
  - `integration -> signing/release` when integration failure causes build/signing errors.
  - `integration -> performance` when performance symptom depends on native integration correctness.
- Cross-skill handoff contract:
  - pass platform, workflow mode, urgency, classification, reproduction context, blocking symptom, constraints, and expected output depth.
- Ambiguity resolution:
  - use bounded assumptions and escalate instead of guessing when classification confidence is low.

## Anti-Patterns
- Attempting to solve integration/signing/profiling inside this orchestration skill.
- Duplicating deep logic already owned by specialized skills.
- Mixing multiple domain problems into one undifferentiated answer.
- Giving release guidance without checking signing setup state.
- Giving performance advice without profiling evidence.
- Suggesting local-only fixes that diverge from CI behavior.

## Decision Tree
- If request is quick and low-risk high-level guidance:
  - provide lightweight guidance.
- If request needs deep execution:
  - classify domain and route.
- If problem involves native module/SDK setup:
  - route to `rn-native-module-integration`.
- If problem involves keystore/provisioning/signing/release artifact readiness:
  - route to `rn-build-signing`.
- If problem involves frame drops/startup latency/memory regressions:
  - route to `rn-performance-profiling`.
- If multiple domains overlap:
  - classify and route sequentially using dependency order.

## Execution Workflow
1. Collect required inputs.
2. Detect workflow mode.
3. Classify request domain.
4. Determine if high-level guidance is sufficient.
5. Route to appropriate specialized skill.
6. Define cross-skill handoff order for mixed-domain cases.
7. Produce structured orchestration output.

## Edge Cases
- Vague request actually spans integration + signing.
- Build/signing issue rooted in failed native module integration.
- Performance regression caused by incorrect native integration path.
- User asks for quick help but issue is release-blocking and needs deep routing.
- Mixed-domain incident where wrong routing order increases blast radius.

## Observability
- Track detected domain classification, platform scope, and urgency.
- Track routing decision and selected specialized skill(s).
- Track whether response stayed high-level or escalated to deep routing.
- Track ambiguous classification cases for refinement.
- Do not log sensitive data.

## Output Contract
- Context Summary
- Workflow Mode
- Problem Classification
- Routing Decision
- Lightweight Guidance (if applicable)
- Specialized Skill Handoff
- Risks / Escalation Notes
- Next Implementation Step

## Verification Checklist
- Workflow mode is identified before recommendations.
- Problem classification is explicit and domain-scoped.
- Routing decision maps to correct specialized skill.
- Mixed-domain ordering is explicit when applicable.
- High-level mode is used only when risk is low.
- Next step is minimal, actionable, and safe.

## Risks / Rollback
- Risk: wrong triage causes delayed fix.
  - Rollback: reclassify by layer and reroute with explicit assumptions.
- Risk: shallow advice for high-risk issue.
  - Rollback: escalate to deep domain handling immediately.
