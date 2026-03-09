# React Native + Expo Skills Marketplace

A public multi-plugin skill repository for AI coding agents, structured similarly to Expo's skills marketplace format.

## What This Repo Provides

- A plugin marketplace index at `.claude-plugin/marketplace.json`
- Multiple installable plugin packs under `plugins/`
- Skill folders with `SKILL.md` frontmatter for auto-discovery and routing
- `agents/openai.yaml` metadata for OpenAI/Codex-compatible interfaces

## Plugin Packs

1. `expo-platform`
Purpose: Senior Expo managed-workflow implementation support.
Covers: Expo architecture, EAS release, config plugins, OTA strategy, permissions.

2. `react-native-cli-platform`
Purpose: Senior bare React Native CLI implementation support.
Covers: Native module integration, signing/release, native build debugging, profiling.

3. `mobile-advanced-controls`
Purpose: Focused micro-skills for advanced architecture and optimization.
Covers: state architecture, navigation, forms, animation, testing, list performance.

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

## Recommended Installation Strategy

- Install `expo-platform` for Expo-first teams.
- Install `react-native-cli-platform` for bare/native-heavy teams.
- Install `mobile-advanced-controls` for high-complexity apps where precision routing matters.

## How Auto-Routing Works

Agents route into skills using `SKILL.md` frontmatter `description` and skill body guidance.
To improve routing quality:
- Keep descriptions specific and non-overlapping.
- Keep skills narrow and composable.
- Put trigger contexts in frontmatter, not only body text.

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

## Development Workflow

1. Edit skills in `plugins/<plugin>/skills/<skill>/SKILL.md`.
2. Validate each skill with quick validator.
3. Keep plugin `README.md` skill catalog in sync.
4. Update `.claude-plugin/marketplace.json` when adding/removing packs.

## Validation

Example validation command:

```bash
python /path/to/quick_validate.py plugins/expo-platform/skills/expo-platform-assistant
```

## Publishing Checklist

- Ensure all skill names are lowercase-hyphenated.
- Ensure every skill validates.
- Ensure `plugin.json` and `marketplace.json` are valid JSON.
- Push to public GitHub repository.
- Verify installation on at least one target agent.
