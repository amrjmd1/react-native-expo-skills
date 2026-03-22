---
name: mobile-accessibility-i18n
description: Execution-grade skill for designing accessible, localization-safe React Native and Expo interfaces with correct semantics, focus behavior, dynamic text resilience, RTL support, and locale-aware formatting.
metadata:
  domain: mobile-user-experience-quality
---

# Skill: mobile-accessibility-i18n

## Purpose

Design and validate mobile UI behavior that is accessibility-correct and localization-safe under real runtime conditions, including screen readers, dynamic text scaling, RTL layout, and locale-aware formatting.

## When to Use

- Designing or refactoring form, checkout, auth, settings, list, modal, or content-heavy screens.
- Implementing custom components that must expose accessible semantics.
- Adding new locale support, RTL support, or translation delivery changes.
- Hardening focus behavior, validation semantics, and navigation transitions.
- Preparing release gates for accessibility and localization-critical flows.

## When Not to Use

- Backend-only localization pipelines with no mobile UI impact.
- Pure visual redesigns that do not alter semantics, text, focus, or localization behavior.
- One-off copy updates with no formatting/layout/semantic impact.

## Required Inputs

- `target_platform`: `ios` | `android` | `both`
- `app_stack`: `expo` | `react-native-cli` | `hybrid`
- `screen_type`: `form` | `list` | `modal` | `settings` | `auth` | `dashboard` | `content-heavy` | `mixed`
- supported locales and default locale
- RTL requirement: required | optional | none
- text scaling / dynamic type requirements
- accessibility preference support: screen reader, reduced motion, increased contrast
- component complexity and custom component usage
- navigation/focus complexity
- content source type: `static` | `cms` | `api-driven` | `mixed`
- formatting needs: dates, times, numbers, currency, pluralization, interpolation
- translation delivery model: `bundled` | `remote` | `mixed`
- release criticality and legal/compliance constraints
- testing coverage confidence for accessibility and localization paths
- analytics/telemetry requirements for accessibility/localization failures

## Framework-Specific Directives

- Treat accessibility semantics as component contract, not optional decoration.
- Define explicit focus order and focus recovery for modals, validation errors, and route transitions.
- Treat dynamic type and translation expansion as mandatory layout stressors.
- Use centralized locale-aware formatting layer; never build formatted strings manually in UI code.
- Treat RTL as full layout mode, not text-direction toggle only.
- Honor reduced-motion and related preferences by adjusting motion behavior where relevant.
- Do not assume iOS VoiceOver and Android TalkBack behavior is identical.
- Validate accessibility/localization behavior in release-like runs, not static inspection only.

## Technical Implementation Patterns

- Accessibility semantics map:
  - Define `role`, `label`, `hint`, `state`, and `value` semantics by component type.
  - Require explicit semantics for custom controls.
- Focus management model:
  - Define initial focus, transition focus, modal focus trap, and focus return behavior.
  - Route validation focus deterministically to first actionable error.
- Dynamic type resilience:
  - Define scaling-safe typography and spacing constraints.
  - Define truncation, wrapping, overflow, and multiline behavior per component.
- RTL and bidirectional layout rules:
  - Define mirrored layouts and explicitly directional content.
  - Replace hard-coded left/right logic with direction-aware rules.
- Localization contract:
  - Centralize translation keys, ownership, and key lifecycle.
  - Ban inline string concatenation where grammar/order can vary by locale.
- Locale-aware formatting layer:
  - Centralize dates, times, numbers, percentages, currency, pluralization, interpolation.
  - Enforce shared formatters across screens for consistency.
- Translation fallback model:
  - Define missing-key fallback behavior per criticality level.
  - Block release for missing critical-locale strings in critical flows.
- Content expansion resilience:
  - Validate long strings, plural variants, and translated CTA lengths against layout constraints.
- Custom component accessibility wrapper pattern:
  - Wrap custom interactions with explicit semantic state and interaction affordances.
- Release validation matrix:
  - Verify critical flows with screen reader, large text, RTL, and at least one non-default locale.

## Anti-Patterns

- Using placeholder text as primary field label.
- Encoding meaning with visual position only.
- Shipping custom components without explicit semantic role/state.
- Hard-coding English grammar or string concatenation.
- Assuming all locales fit default string lengths.
- Assuming LTR in spacing, icons, gestures, or motion direction.
- Ignoring focus recovery after modal close, errors, or navigation changes.
- Treating accessibility as post-release polish.
- Formatting numbers/dates/currency manually inside view code.
- Hiding actionable controls from assistive technology without explicit reason.

## Decision Tree

- If element is built-in text/control and semantics are sufficient:
  - Use native semantics with explicit label/state validation.
- If element is custom/compound control:
  - Apply custom semantics map and accessibility wrapper pattern.
- If issue is semantics-only:
  - Prioritize role/label/state correctness and screen-reader path validation.
- If issue is localization-only:
  - Prioritize translation keys, formatting layer, fallback behavior.
- If issue is mixed semantics + localization:
  - Resolve semantics contract first, then localization/formatting path.
- If RTL is required:
  - Enable bidirectional layout rules and validate mirrored interactions.
- If dynamic type is large scale:
  - Apply resilience constraints before visual tuning.
- If focus flow includes modal or error recovery:
  - Apply explicit focus model with deterministic return targets.
- If screen is release-critical:
  - Enforce full validation matrix and strict release gating.
- If screen is lower risk:
  - Use scoped validation but keep fallback and semantics contracts enforced.

## Execution Workflow

1. Collect required inputs.
2. Classify screen type, interaction model, and locale/accessibility requirements.
3. Define accessibility semantics and custom component contracts.
4. Define focus order, focus recovery, and error focus behavior.
5. Define localization boundaries, formatting ownership, and fallback rules.
6. Define RTL and dynamic type resilience strategy.
7. Define validation matrix for critical flows and supported locales.
8. Define observability signals for accessibility/localization failures.
9. Validate UX determinism under scaling, translation, and assistive-technology conditions.
10. Produce structured output.

## Edge Cases

- Screen reader reads decorative content as actionable.
- Custom control is focusable but semantically incorrect.
- Modal closes and focus target is lost.
- Validation error is visible but not exposed semantically.
- Large text breaks CTA layout or hides actionable controls.
- Translated string truncates critical meaning.
- Pluralization/interpolation produces invalid grammar in locale.
- RTL mirrors layout incorrectly or leaves icons misaligned.
- Runtime locale change leaves stale labels or stale formatting.
- Remote translations load late and mismatch initial state.
- Accessibility preference changes after screen mount.
- VoiceOver behavior is correct while TalkBack path fails.

## Observability

- Track accessibility issue reports by screen and component type.
- Track focus recovery failures and unexpected focus loss events.
- Track missing translation key and fallback key usage rates.
- Track locale formatting failures by formatter type.
- Track large-text layout breakage signals on critical screens.
- Track RTL rendering regressions and direction mismatch events.
- Track screen-reader path failures for critical journeys.
- Track translation load failures and stale locale-state incidents.
- Track assistive-technology-related drop-off on critical flows.
- Never log raw user text input, translated content payloads, or sensitive user data.

## Output Contract

- Context Summary
- Assumptions
- Accessibility Semantics Map
- Focus / Navigation Rules
- Localization / Formatting Strategy
- RTL / Dynamic Type Resilience Rules
- Validation Matrix
- Observability Signals
- Risks / Rollback
- Next Implementation Step

## Verification Checklist

- Interactive elements expose correct semantic role, label, and state.
- Custom components explicitly expose accessible behavior.
- Focus order and focus recovery are deterministic.
- Validation/error states are semantically accessible, not only visible.
- Critical screens remain usable with large text and long translations.
- Locale-aware formatting is centralized and correct.
- RTL behavior is validated where required.
- Missing-translation fallback behavior is defined and tested.
- At least one critical flow is validated with assistive-technology assumptions.
- Observability signals exist for accessibility/localization regressions.

## Risks / Rollback

- Risks:
  - Inaccessible critical interaction flow.
  - Broken focus path after modal/error/navigation transition.
  - Localization regression in critical locale.
  - RTL layout breakage in key screens.
  - Large-text layout collapse.
  - Semantic drift in custom components.
  - Formatting or translation fallback failure.
- Rollback rules:
  - Keep previous known-good localization bundles and UI release candidate.
  - Define rollback triggers for critical accessibility/localization regressions.
  - Roll back high-risk screens/flows first if full rollback is not required.
  - Block further promotion until semantics, focus, and locale formatting regressions are resolved.

## Example

- Scenario:
  - Bilingual app (`en`, `ar`) with auth form as release-critical flow.
  - Requirements: screen reader support, large text, RTL, translated validation errors, locale-aware date/number formatting.
- Execution:
  - Define semantics map for fields/buttons/errors and custom password visibility control.
  - Define focus order and deterministic focus return after modal-based error help.
  - Centralize translations and formatting; enforce fallback for missing critical keys.
  - Validate matrix: VoiceOver/TalkBack path, large text at max scale, RTL layout, non-default locale formatting.
- Release gate:
  - Block release on missing critical translations, broken error semantics, focus recovery failure, or RTL layout breakage.
