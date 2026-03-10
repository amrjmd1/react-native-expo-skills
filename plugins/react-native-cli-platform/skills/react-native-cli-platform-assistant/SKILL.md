---
name: react-native-cli-platform-assistant
description: Execution-grade orchestration skill for React Native CLI platform triage, high-level guidance, and routing to specialized native platform skills.
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
- Workflow and project type (RN CLI, architecture mode, Hermes/new architecture).
- Target platform (`ios`, `android`, `both`).
- Request type (triage, quick guidance, implementation planning, release-blocking issue).
- Urgency and production impact.
- Current blocking symptom and reproduction scope.
- Known constraints (CI, signing, SDK versions, deadlines).

## Framework-Specific Directives
- React Native CLI (default):
  - Treat native folders as source of truth and validate toolchain parity first.
- RN CLI + EAS/CI integration:
  - Keep environment and build-path assumptions explicit and CI-aligned.
- Bare-native-heavy projects:
  - Separate RN-owned fixes from platform-owned native changes.

## Technical Implementation Patterns
- Use a triage matrix: layer (`JS`, `bridge`, `native build`, `runtime`, `release`) x severity.
- Route single-domain issues directly to the owning specialized path.
- Split mixed-domain incidents into ordered sub-problems before proposing changes.
- Keep orchestration output short: classification, route, immediate next action.

## Anti-Patterns
- Giving deep domain advice without first classifying layer and ownership.
- Combining build, runtime, and release concerns in one vague recommendation.
- Suggesting local-only fixes that diverge from CI behavior.

## Decision Tree
- If issue is quick and low-risk:
  - provide lightweight guidance.
- If issue is implementation-specific or release-blocking:
  - route to specialized domain handling.
- If issue is mixed-domain:
  - split and order domains, then route sequentially.

## Execution Workflow
1. Collect required inputs.
2. Classify problem layer and urgency.
3. Determine platform/toolchain ownership boundaries.
4. Decide lightweight guidance vs deep routing.
5. Define ordered handoff for mixed-domain work.
6. Produce structured orchestration output.

## Edge Cases
- Same symptom has different root causes on iOS vs Android.
- Local fix works but CI remains broken.
- Build issue masked by runtime fallback behavior.
- Mixed-domain incident where wrong first step increases blast radius.

## Observability
- Track detected layer, platform scope, and urgency.
- Track routing decisions and escalation frequency.
- Track ambiguous triage cases for refinement.

## Output Contract
- Context Summary
- Assumptions
- Architecture / Design
- Implementation Steps
- Verification Checklist
- Risks / Rollback
- Next Implementation Step

## Verification Checklist
- Problem layer and ownership are explicit.
- Routing decision is deterministic.
- Platform-specific constraints are acknowledged.
- Next step is minimal, actionable, and safe.

## Risks / Rollback
- Risk: wrong triage causes delayed fix.
  - Rollback: reclassify by layer and reroute with explicit assumptions.
- Risk: shallow advice for high-risk issue.
  - Rollback: escalate to deep domain handling immediately.
