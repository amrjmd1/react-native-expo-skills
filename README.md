# React Native + Expo Skills Marketplace

A public multi-plugin skill repository for AI coding agents, structured similarly to Expo's skills marketplace format.

## Plugin Packs

- `expo-platform`: Expo architecture, EAS release, config plugins, OTA, permissions.
- `react-native-cli-platform`: bare RN CLI, native integration, signing, profiling.
- `mobile-advanced-controls`: state, navigation, forms, animation, testing, list performance.

## Installation

### Claude Code

1. Add marketplace:

```bash
/plugin marketplace add amrjmd1/react-native-expo-skills
```

2. Install packs (all or selected):

```bash
/plugin install expo-platform
/plugin install react-native-cli-platform
/plugin install mobile-advanced-controls
```

### Cursor

Add this repository as a Remote Rule in Cursor settings.

### Any agent (skills CLI)

```bash
bunx skills add amrjmd1/react-native-expo-skills
```

## How Auto-Routing Works

Agents route into skills using `SKILL.md` descriptions and body instructions. Skills are narrow by design so the agent can choose a precise specialist instead of one broad prompt.

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
- Buy Me a Coffee: [buymeacoffee.com/devamrdar](https://buymeacoffee.com/devamrdar)
