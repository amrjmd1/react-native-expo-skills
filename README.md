# React Native + Expo Skills Marketplace

![skills](https://img.shields.io/badge/skills-engineering-blue)
![domains](https://img.shields.io/badge/domains-11-purple)
![license](https://img.shields.io/badge/license-MIT-lightgrey)
![status](https://img.shields.io/badge/status-active-success)

Structured AI engineering skills for React Native and Expo development.

## Quick Navigation

- [What This Project Is](#what-this-project-is)
- [Why This Project Exists](#why-this-project-exists)
- [Who This Project Is For](#who-this-project-is-for)
- [Quick Start](#quick-start)
- [Recommended Usage Modes](#recommended-usage-modes)
- [How to Start](#how-to-start)
- [How the Skill System Works](#how-the-skill-system-works)
- [Installation](#installation)
- [Plugin Domains Overview](#plugin-domains-overview)
- [Example Skills](#example-skills)
- [Example Agent Usage](#example-agent-usage)
- [Skill Quality System](#skill-quality-system)
- [Repository Structure](#repository-structure)
- [Contributing](#contributing)
- [Roadmap](#roadmap)

---

## What This Project Is

This repository is a public multi-plugin skill system for AI coding agents working on React Native and Expo codebases.

It contains installable AI agent skills. Each skill provides a deterministic engineering workflow that helps an agent perform real development tasks such as:

- architecture decisions
- implementation planning
- production debugging
- release and operations workflows
- validation and rollback analysis

These skills are written as execution systems, not generic documentation. They are intended to produce consistent, reviewable engineering output.

---

## Why This Project Exists

AI coding assistants often produce inconsistent advice for the same engineering problem. That is a poor fit for production mobile systems.

This repository exists to encode deterministic engineering knowledge for React Native and Expo teams:

- repeatable workflows instead of vague suggestions
- architecture and diagnostics guidance grounded in real mobile constraints
- implementation rules that are easier to review, verify, and reuse
- production-safe patterns for security, reliability, delivery, and platform operations

---

## Who This Project Is For

- mobile teams using AI coding assistants
- React Native architects
- Expo production teams
- teams that want deterministic engineering guidance
- engineers building AI development workflows
- teams that want fast planning plus deeper execution

---

## Quick Start

- For fast project planning  
  Install `mobile-platform-orchestrator`
- For production-grade workflows  
  Install `mobile-platform-orchestrator` plus the relevant specialist packs such as `expo-platform`, `mobile-data-platform`, `mobile-identity-security`, and `mobile-observability-reliability`
- For focused deep work  
  Install only the specialist pack that matches the domain you already know

This repository is designed as a structured AI engineering skill system. The orchestrator is the fastest entry point for planning, while specialist packs provide the execution depth required for production work.

---

## Recommended Usage Modes

- **Orchestrator-first**  
  Best for new projects, architecture planning, and fast system setup
- **Specialist-first**  
  Best for focused implementation or debugging in a known domain
- **Combined mode**  
  Best for production workflows: use `mobile-platform-orchestrator` for planning and routing, then use specialist skills for execution depth

---

## How to Start

1. Start with `mobile-platform-orchestrator`
2. Let it identify the required domains and skills
3. Use the selected specialist skills for implementation depth
4. Combine the outputs into a production-ready plan

---

## How the Skill System Works

The intended lifecycle is:

User problem  
↓  
AI agent selects a relevant skill  
↓  
The skill provides a deterministic workflow  
↓  
The agent produces structured engineering output

Each skill is expected to contain:

- purpose
- decision tree
- execution workflow
- anti-patterns
- observability guidance
- verification checklist or matrix
- rollback and risk handling

This structure helps different agents reach similar conclusions for the same engineering problem.

---

## Installation

You can install either:

- the orchestrator plugin for planning, intake, and skill routing
- specialist plugin packs for deep domain execution
- both together for the strongest production setup

Practical guidance:

- `mobile-platform-orchestrator` alone is the fastest way to start planning a mobile system
- `mobile-platform-orchestrator` plus specialist packs gives full planning and execution coverage
- specialist packs alone work best for focused deep work in a known domain

### Claude Code

1. Add the marketplace:

```bash
/plugin marketplace add amrjmd1/react-native-expo-skills
```

2. Install the orchestrator or any specialist packs:

```bash
/plugin install mobile-platform-orchestrator
/plugin install mobile-platform-architecture
/plugin install expo-platform
/plugin install react-native-cli-platform
/plugin install mobile-advanced-controls
/plugin install mobile-identity-security
/plugin install mobile-data-platform
/plugin install mobile-observability-reliability
/plugin install mobile-user-experience-quality
/plugin install mobile-revenue-systems
/plugin install mobile-delivery-lifecycle
```

Common examples:

```bash
/plugin install mobile-platform-architecture
/plugin install expo-platform
/plugin install mobile-data-platform
```

### Cursor

Add this repository as a Remote Rule in Cursor settings.

### Any agent (skills CLI)

```bash
bunx skills add amrjmd1/react-native-expo-skills
```

---

## Plugin Domains Overview

- `mobile-platform-orchestrator`  
  Planning and coordination entry point that selects and sequences domain skills. It does not replace specialist execution depth.
- `mobile-platform-architecture`  
  Monorepo structure, internal package ownership, shared platform boundaries, and versioning strategy.
- `expo-platform`  
  Expo-specific architecture, config plugins, permissions, OTA, and EAS release workflows.
- `react-native-cli-platform`  
  Bare React Native CLI architecture, native integration, build signing, and profiling.
- `mobile-advanced-controls`  
  Advanced UI architecture, navigation, state, forms, testing, animation, and list performance.
- `mobile-identity-security`  
  Authentication lifecycle, secure storage, biometrics, session integrity, and threat-aware mobile auth design.
- `mobile-data-platform`  
  API/query architecture, offline sync, conflict resolution, and realtime communication patterns.
- `mobile-observability-reliability`  
  Telemetry, crash and alert strategy, rollback decisions, and incident response workflows.
- `mobile-user-experience-quality`  
  Accessibility, internationalization, background behavior, and notification reliability.
- `mobile-revenue-systems`  
  Payments, subscriptions, entitlement integrity, and billing recovery flows.
- `mobile-delivery-lifecycle`  
  CI/CD workflows, release governance, migration planning, and upgrade execution.

---

## Example Skills

Some representative skills in the repository:

- `rn-list-performance-control`  
  Diagnoses virtualization, pagination, and render churn problems in large React Native lists.
- `mobile-auth-session-security`  
  Designs secure session lifecycle, refresh behavior, revocation, and token handling.
- `expo-updates-ota`  
  Manages OTA update strategy, channel rollout safety, and runtime compatibility.
- `rn-animations-reanimated`  
  Guides performant motion architecture and interaction-safe animation patterns.
- `mobile-cicd-workflows`  
  Structures deterministic mobile CI/CD pipelines with gates, secret handling, and rollback controls.
- `mobile-offline-sync-conflicts`  
  Handles offline queues, sync retries, idempotency, and conflict resolution under unstable connectivity.

For the full index, see [docs/SKILLS_INDEX.md](docs/SKILLS_INDEX.md).
For problem-first selection, see [docs/SKILL_ROUTING.md](docs/SKILL_ROUTING.md).

---

## Example Agent Usage

Planning flow:

- User wants a mid-size app with auth, payments, offline sync, and analytics
- Agent starts with `mobile-platform-orchestrator`
- The orchestrator selects:
  - `mobile-auth-session-security`
  - `mobile-offline-sync-conflicts`
  - `mobile-payments-subscriptions`
  - `mobile-observability-telemetry`
- The user then uses those specialist skills for implementation depth and system-specific decisions

Focused debugging flow:

- User request: "My FlatList drops frames on Android."
- Agent loads `rn-list-performance-control`
- Skill workflow:
  1. classify list workload
  2. isolate render boundaries
  3. validate virtualization configuration
  4. compare debug vs release performance
  5. propose measurable remediation and verification steps

---

## Recommended Install Paths

- Expo production teams  
  `mobile-platform-architecture`, `mobile-platform-orchestrator`, `expo-platform`, `mobile-advanced-controls`, `mobile-identity-security`, `mobile-data-platform`, `mobile-observability-reliability`, `mobile-delivery-lifecycle`
- Bare React Native CLI production teams  
  `mobile-platform-architecture`, `mobile-platform-orchestrator`, `react-native-cli-platform`, `mobile-advanced-controls`, `mobile-identity-security`, `mobile-data-platform`, `mobile-observability-reliability`, `mobile-delivery-lifecycle`
- Full enterprise stack  
  Install all plugin packs

---

## Skill Quality System

This repository uses explicit quality gates for every new or updated skill.

The discovery and execution system is organized across:

- [docs/SKILLS_INDEX.md](docs/SKILLS_INDEX.md)  
  Fast domain-based discovery of available skills
- [docs/SKILL_ROUTING.md](docs/SKILL_ROUTING.md)  
  Problem-first routing from engineering issue to skill selection

- [docs/SKILL_TEMPLATE.md](docs/SKILL_TEMPLATE.md)  
  Defines the required shape and sections for every skill.
- [docs/SKILL_REVIEW_CHECKLIST.md](docs/SKILL_REVIEW_CHECKLIST.md)  
  Defines the structural, technical, and agent-usability review criteria.
- [docs/SKILL_SCORING.md](docs/SKILL_SCORING.md)  
  Defines the scoring model and production-readiness thresholds.

Skills are expected to be concise, deterministic, implementation-focused, and reviewable. New or updated skills should be reviewed against the checklist and scored before acceptance.

---

## Repository Structure

```text
.claude-plugin/
  marketplace.json
plugins/
  <plugin>/
    .claude-plugin/
      plugin.json
    README.md
    skills/
      <skill>/
        SKILL.md
        agents/
          openai.yaml
docs/
  SKILLS_INDEX.md
  SKILL_TEMPLATE.md
  SKILL_REVIEW_CHECKLIST.md
  SKILL_SCORING.md
```

Plugins group related domains into specialist packs. Each skill is implemented as a `SKILL.md` plus agent-specific metadata.

In practice:

- use `mobile-platform-orchestrator` as the coordination layer when planning spans domains
- use specialist packs when implementation depth is required
- combine both for production-ready workflows

---

## Contributing

To add or improve a skill:

1. start from [docs/SKILL_TEMPLATE.md](docs/SKILL_TEMPLATE.md)
2. review it against [docs/SKILL_REVIEW_CHECKLIST.md](docs/SKILL_REVIEW_CHECKLIST.md)
3. score it with [docs/SKILL_SCORING.md](docs/SKILL_SCORING.md)
4. place it under `plugins/<plugin>/skills/<skill>/SKILL.md`
5. update the relevant plugin README
6. update `.claude-plugin/marketplace.json` if a new plugin is added
7. submit a pull request

See [CONTRIBUTING.md](CONTRIBUTING.md) for repository authoring, validation, and release workflow details.

---

## Roadmap

- expand skill domain coverage across additional production mobile workflows
- improve skill routing and discovery across plugin packs
- add more golden examples for common engineering incidents
- strengthen cross-skill orchestration between broad intake and specialist execution

---

## Contact

- Gravatar: [gravatar.com/devamrdar](https://gravatar.com/devamrdar)
- GitHub: [github.com/amrjmd1](https://github.com/amrjmd1)
- LinkedIn: [linkedin.com/in/amrjmd1](https://www.linkedin.com/in/amrjmd1)
- X: [x.com/devamrdar](https://x.com/devamrdar)

## Support

If this project is useful to you, you can support the work here:

- Buy Me a Coffee: [buymeacoffee.com/devamrdar](https://buymeacoffee.com/devamrdar)

---

## Closing

This repository aims to build a deterministic engineering knowledge system for AI-assisted mobile development.
