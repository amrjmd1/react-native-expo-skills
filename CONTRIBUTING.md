# Contributing

## Scope

This repository publishes installable mobile skill plugins. Keep changes focused on:
- skill quality (`SKILL.md` content),
- plugin metadata (`plugin.json`),
- marketplace index (`.claude-plugin/marketplace.json`),
- docs consistency.

## Authoring Rules

- Keep skill names lowercase-hyphenated.
- Put trigger contexts in `SKILL.md` frontmatter `description`.
- Keep each skill focused on one domain/problem class.
- Prefer explicit workflows and output contracts over vague advice.
- Keep recommendations compatible with current Expo/RN practices.
- Keep skills deterministic and execution-oriented, not generic documentation.
- Use only supported frontmatter fields in `SKILL.md`:
  - `name`
  - `description`
  - `metadata.domain`

## Skill Workflow (Required)

Use this repository's skill workflow docs for every new or updated skill:

1. Start from [`docs/SKILL_TEMPLATE.md`](docs/SKILL_TEMPLATE.md).
2. Review against [`docs/SKILL_REVIEW_CHECKLIST.md`](docs/SKILL_REVIEW_CHECKLIST.md).
3. Score with [`docs/SKILL_SCORING.md`](docs/SKILL_SCORING.md).
4. Keep the final skill concise, deterministic, and implementation-focused.

## Updating or Adding a Skill

1. Edit or create:
   - `plugins/<plugin>/skills/<skill-name>/SKILL.md`
   - `plugins/<plugin>/skills/<skill-name>/agents/openai.yaml`
2. Update plugin README skill list.
3. If adding a new plugin, add it to `.claude-plugin/marketplace.json`.

## Validation Checklist

- Validate skill folder schema using your local skill validator tooling.
- Ensure `plugin.json` and `marketplace.json` are valid JSON.
- Ensure there are no stale references after renames/moves.
- Ensure install instructions in README still match actual plugin names.

## Release Checklist

- Run full repository review for naming and trigger overlap.
- Confirm every plugin listed in marketplace exists and has a manifest.
- Confirm every skill folder contains both `SKILL.md` and `agents/openai.yaml`.
- Verify installation on at least one target agent before publishing.

## Pull Request Guidance

Include in each PR:
- what changed,
- which skills/plugins are affected,
- how routing behavior is expected to improve,
- what validation was run.
