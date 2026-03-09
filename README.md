# React Native + Expo Skills Marketplace

A public multi-plugin skill repository for AI coding agents, structured similarly to Expo's skills marketplace format.

## Plugin Packs

- `mobile-platform-orchestrator`: one comprehensive entry skill that routes to specialists.
- `expo-platform`: Expo architecture, EAS release, config plugins, OTA, permissions.
- `react-native-cli-platform`: bare RN CLI, native integration, signing, profiling.
- `mobile-advanced-controls`: state, navigation, forms, animation, testing, list performance.
- `mobile-identity-security`: auth lifecycle, secure storage, biometrics, session hardening.
- `mobile-data-platform`: API/query architecture, offline sync/conflicts, websocket/realtime.
- `mobile-observability-reliability`: telemetry, crash analytics, alerting, incident response.
- `mobile-platform-architecture`: monorepo/workspaces, shared UI kits, package versioning.
- `mobile-user-experience-quality`: accessibility, i18n/l10n, background tasks, notifications.
- `mobile-revenue-systems`: payments, subscriptions, entitlements, billing recovery.
- `mobile-delivery-lifecycle`: CI/CD workflows and RN/Expo migration/upgrade playbooks.

## Skill Coverage Matrix

- Unified Entry: `mobile-platform-orchestrator`
- Architecture Core: `expo-platform`, `react-native-cli-platform`, `mobile-platform-architecture`
- Product Quality: `mobile-advanced-controls`, `mobile-user-experience-quality`
- Data and Reliability: `mobile-data-platform`, `mobile-observability-reliability`
- Security and Revenue: `mobile-identity-security`, `mobile-revenue-systems`
- Delivery and Evolution: `mobile-delivery-lifecycle`

## Recommended Install Paths

1. Expo production teams:
   - `mobile-platform-orchestrator`
   - `expo-platform`
   - `mobile-advanced-controls`
   - `mobile-identity-security`
   - `mobile-data-platform`
   - `mobile-observability-reliability`
   - `mobile-delivery-lifecycle`

2. Bare RN CLI production teams:
   - `mobile-platform-orchestrator`
   - `react-native-cli-platform`
   - `mobile-advanced-controls`
   - `mobile-identity-security`
   - `mobile-data-platform`
   - `mobile-observability-reliability`
   - `mobile-delivery-lifecycle`

3. Full enterprise stack:
   - all plugin packs

## Two Usage Modes

1. Single comprehensive mode:
   - Install `mobile-platform-orchestrator`
   - Best for teams that want one entry point and guided triage.

2. Specialist precision mode:
   - Install domain plugins directly (`security`, `data`, `observability`, `delivery`, etc.)
   - Best for teams that want maximal depth and direct routing.

Best production setup:
- install orchestrator + specialist packs together.
- use orchestrator for intake and planning.
- use specialist skills for deep execution.

## Installation

### Claude Code

1. Add marketplace:

```bash
/plugin marketplace add amrjmd1/react-native-expo-skills
```

2. Install packs (all or selected):

```bash
/plugin install mobile-platform-orchestrator
/plugin install expo-platform
/plugin install react-native-cli-platform
/plugin install mobile-advanced-controls
/plugin install mobile-identity-security
/plugin install mobile-data-platform
/plugin install mobile-observability-reliability
/plugin install mobile-platform-architecture
/plugin install mobile-user-experience-quality
/plugin install mobile-revenue-systems
/plugin install mobile-delivery-lifecycle
```

### Cursor

Add this repository as a Remote Rule in Cursor settings.

### Any agent (skills CLI)

```bash
bunx skills add amrjmd1/react-native-expo-skills
```

## How Auto-Routing Works

Agents route into skills using `SKILL.md` descriptions and body instructions. Skills are narrow by design so the agent can choose a precise specialist instead of one broad prompt.

## Strengths and Developer Recommendations

### Strengths
- High flexibility: choose one comprehensive skill or multiple specialist skills.
- Higher precision: domain specialization reduces overlap and improves solution quality.
- Scalable structure: new domains can be added without breaking existing packs.
- Production quality: each skill is designed with senior-level quality, risk, and verification standards.

### Recommendations for Developers
- If your team is new to the stack, start with `mobile-platform-orchestrator`.
- If you have a deep domain challenge (security/data/payments), use specialist skills directly.
- For production projects, install orchestrator + specialist packs together.
- For critical tasks, always require a verification plan and rollback plan.

## Repository Structure

```text
.claude-plugin/marketplace.json
plugins/
  <plugin-name>/
    .claude-plugin/plugin.json
    README.md
    skills/
      <skill-name>/
        SKILL.md
        agents/openai.yaml
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for authoring, validation, and release workflow.

## Contact

Stay in touch:
- Gravatar profile: [gravatar.com/devamrdar](https://gravatar.com/devamrdar)
- GitHub: [github.com/amrjmd1](https://github.com/amrjmd1)
- LinkedIn: [linkedin.com/in/amrjmd1](https://www.linkedin.com/in/amrjmd1)
- X: [x.com/devamrdar](https://x.com/devamrdar)

## Support

If this project helps you, you can support my work:
- Buy Me a Coffee: [buymeacoffee.com/devamrdar](https://buymeacoffee.com/devamrdar) ☕️
