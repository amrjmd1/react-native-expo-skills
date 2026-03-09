---

## Required Skill Metadata

Every skill must begin with a YAML frontmatter block that describes the skill.

This metadata allows tools, agents, and future automation to discover and index skills.

Required fields:

```yaml
---
name: <skill-id>
description: <short execution-focused description>
metadata:
  domain: <skill-domain>
---
```

Field rules:

- **name**: stable identifier using lowercase and hyphen-separated words.
- **description**: one concise sentence describing what the skill executes (not a generic definition).
- **metadata.domain**: logical grouping such as `mobile-identity-security`, `mobile-networking`, `mobile-performance`, etc. This must be placed inside the supported `metadata` field.

### Skill Naming Convention

- Use lowercase, hyphen-separated identifiers only.
- Keep names stable once published.
- Prefer domain-explicit patterns such as:
  - `mobile-auth-session-security`
  - `mobile-biometric-secure-storage`
  - `mobile-oauth-session-flow`

Example:

```yaml
---
name: mobile-auth-session-security
description: Execution-grade skill for designing secure token lifecycle and session integrity in React Native and Expo apps.
metadata:
  domain: mobile-identity-security
---
```

---

# SKILL_TEMPLATE

This template defines the required structure for every skill in this repository.

All skills must follow this format to ensure consistency, quality, and reliable behavior when used by AI agents.

Do not remove sections unless there is a strong technical reason.

---

```yaml
---
name: <skill-id>
description: <short execution description>
metadata:
  domain: <skill-domain>
---
```

# Skill: <skill-name>

## Purpose

Explain the specific engineering problem this skill solves.

Guidelines:

- Be concise
- Describe the real system problem
- Avoid generic statements
- Focus on practical engineering value

Example:

"Design and implement secure session management for mobile applications using Expo or React Native."

---

## When to Use

Describe concrete scenarios where this skill should be applied.

Examples:

- Implementing a new system feature
- Hardening an existing system
- Designing architecture for a specific subsystem

Avoid vague phrases like "when needed".

---

## When Not to Use

Explain when this skill should NOT be applied.

This prevents misuse and keeps agents from applying the wrong skill.

Examples:

- UI-only work
- backend-only systems
- unrelated platform contexts

---

## Required Inputs

List all information the agent must gather before executing the skill.

Examples:

- Platform
- Framework
- Provider
- Security level
- Environment

Rules:

- Inputs must be explicit
- Missing inputs must trigger assumptions

---

## Framework-Specific Directives

Describe how the implementation differs depending on framework or platform.

Examples:

- Expo vs Bare React Native
- Node vs Serverless
- Web vs Mobile

Include recommended libraries when appropriate.

Example:

```
Expo Managed
- expo-secure-store
- expo-auth-session
```

---

## Provider-Specific Directives

Describe behavior differences depending on external services.

Examples:

- Firebase
- Supabase
- Auth0
- Stripe
- Custom backend

Include integration rules and constraints.

---

## Technical Implementation Patterns

Describe recommended architecture patterns.

Examples:

- context/provider patterns
- service layer patterns
- dependency boundaries

Explain responsibilities and expected structure.

---

## Anti-Patterns

List practices that must never be implemented.

Examples:

- insecure storage
- race conditions
- bypassing framework guarantees

These help prevent dangerous implementations.

---

## Decision Tree

Provide conditional logic that determines implementation paths.

Example structure:

```
If Platform == Expo
  → Use expo-secure-store

If Platform == Bare React Native
  → Use react-native-keychain
```

This section ensures agents make deterministic decisions.

---

## Execution Workflow

Provide the ordered implementation process.

Example:

1. Collect inputs
2. Identify constraints
3. Select architecture
4. Implement components
5. Add safety mechanisms
6. Validate behavior

Each step must represent a real engineering action.

---

## Edge Cases

List real-world failure scenarios the implementation must handle.

Examples:

- process termination
- network failures
- corrupted local state
- concurrent requests

Each edge case should imply a recovery strategy.

---

## Observability

Define logs, metrics, or signals needed to debug the system.

Examples:

- success events
- failure events
- retry attempts

Rules:

- never log secrets
- log enough metadata for debugging

---

## Output Contract

Define the structure of the result produced by the skill.

Typical sections:

1. Context Summary
2. Assumptions
3. Architecture Choice
4. Constraints
5. Lifecycle Plan
6. Storage Policy
7. State Machine
8. Failure Handling
9. Verification Checklist
10. Next Implementation Step

This ensures consistent outputs across agents.

---

## Verification Checklist

Define checks that confirm the implementation is correct.

Examples:

- secure storage used
- race conditions prevented
- state transitions defined

This section should allow reviewers to validate correctness.

---

## Risks / Rollback

Describe potential risks and how to recover.

Examples:

- invalid migrations
- broken sessions
- inconsistent state

Include rollback actions.

---

## Example Request

Provide a realistic scenario input.

Example:

```
Platform: Expo Managed
Provider: Supabase
Security Level: Standard
```

---

## Example Response Shape

Provide the expected output structure.

Example:

```
Context Summary
Session Model
Storage Policy
State Machine
Failure Handling
Next Step
```

This ensures the skill produces predictable outputs.

---

## Author Notes (Optional)

Optional section for maintainers to document assumptions or design decisions.

Do not include speculative or theoretical discussion.
