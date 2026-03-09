# SKILL_REVIEW_CHECKLIST

This document defines the standard review process for evaluating any Skill before it is accepted into the repository.

Every Skill must pass this checklist to ensure:

- consistency
- technical depth
- agent usability
- production readiness

If a skill fails multiple checks, it should be revised before being merged.

---

# 1. Structure Review

A valid skill must contain all required sections.

Checklist:

- [ ] Purpose clearly explains the engineering problem
- [ ] When to Use contains concrete scenarios
- [ ] When Not to Use prevents misuse
- [ ] Required Inputs are clearly defined
- [ ] Framework-Specific Directives exist when relevant
- [ ] Provider-Specific Directives exist when relevant
- [ ] Technical Implementation Patterns are present
- [ ] Anti-Patterns are documented
- [ ] Decision Tree contains real branching logic
- [ ] Execution Workflow is step-by-step and actionable
- [ ] Edge Cases reflect real production scenarios
- [ ] Observability guidance exists
- [ ] Output Contract is structured and deterministic
- [ ] Verification Checklist is present
- [ ] Risks / Rollback section exists
- [ ] Example Request is realistic
- [ ] Example Response Shape matches the Output Contract

A skill missing multiple required sections should not be accepted.

---

# 2. Agent Usability Review

The skill must guide an AI agent toward deterministic behavior.

Checklist:

- [ ] The skill tells the agent what inputs to collect
- [ ] The skill reduces ambiguity
- [ ] The skill provides clear implementation choices
- [ ] The skill includes decision rules
- [ ] The skill defines failure handling
- [ ] The skill produces predictable outputs

Goal:

Two different agents using the skill should reach similar conclusions.

---

# 3. Technical Depth Review

The skill must provide real technical guidance, not just theory.

Checklist:

- [ ] The skill references concrete tools or libraries when relevant
- [ ] The skill includes framework-specific behavior
- [ ] The skill includes architecture guidance
- [ ] The skill includes implementation constraints
- [ ] The skill includes anti-pattern warnings

Red flag examples:

- vague "consider" statements
- generic best practices
- no concrete technical direction

---

# 4. Mobile / Platform Realism Review

For platform-specific skills (like mobile or backend), the skill must reflect real runtime conditions.

Checklist:

- [ ] Handles process termination
- [ ] Handles network failure
- [ ] Handles concurrency or race conditions
- [ ] Handles corrupted or missing local state
- [ ] Considers platform constraints (iOS/Android etc.)

Skills that ignore runtime realities are incomplete.

---

# 5. Edge Case Coverage

A strong skill anticipates failures.

Checklist:

- [ ] Edge cases represent real-world failures
- [ ] Recovery behavior is implied or described
- [ ] Edge cases include concurrency or state corruption where relevant

Weak skills only describe the "happy path".

---

# 6. Output Contract Review

The output contract determines how predictable the skill output is.

Checklist:

- [ ] Output sections are clearly defined
- [ ] Output structure is consistent
- [ ] Assumptions are surfaced
- [ ] The result ends with a concrete Next Implementation Step

Weak output contracts produce vague results.

---

# 7. Red Flags

If any of these are present, the skill likely needs revision.

- [ ] The skill reads like documentation instead of an execution system
- [ ] The skill lacks a decision tree
- [ ] The skill provides no concrete technical directives
- [ ] The skill ignores failure paths
- [ ] The skill contains generic advice without operational impact

Skills with multiple red flags should not be accepted.

---

# 8. Skill Scoring

Reviewers should score each dimension.

| Dimension | Score (1–10) |
|----------|--------------|
| Structure | |
| Agent Usability | |
| Technical Depth | |
| Platform Realism | |
| Edge Case Coverage | |
| Output Contract Quality | |

Total Score: ____ / 10

---

# 9. Acceptance Criteria

A skill is considered production-ready when:

- Structure score ≥ 9
- Agent usability ≥ 9
- Technical depth ≥ 8
- Edge-case coverage ≥ 8
- Overall score ≥ 9

Skills below these thresholds should be revised.

---

# 10. Reviewer Notes

Use this section to document review findings.

Strengths:


Weaknesses:


Missing technical directives:


Missing edge cases:


Required improvements:
