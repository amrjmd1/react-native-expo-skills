# SKILL_SCORING

This document defines the scoring system used to evaluate the overall quality and readiness of a Skill.

The scoring model ensures that every Skill in the repository maintains a high standard of:

- structural clarity
- technical depth
- agent usability
- real-world reliability

Scores are used during Skill review and must accompany the checklist defined in `SKILL_REVIEW_CHECKLIST.md`.

---

# 1. Scoring Overview

Each Skill is evaluated across several quality dimensions.

Each dimension is scored from **1 to 10**.

Where:

| Score | Meaning |
|------|--------|
| 1–3 | Poor / unusable |
| 4–5 | Weak |
| 6–7 | Acceptable but incomplete |
| 8 | Strong |
| 9 | Production ready |
| 10 | Exceptional |

---

# 2. Scoring Dimensions

## Structure

Evaluates whether the Skill follows the required template.

High score indicators:

- All required sections exist
- Sections are clearly written
- The structure is consistent with `SKILL_TEMPLATE.md`

Low score indicators:

- Missing sections
- Disorganized structure
- Sections combined or unclear

---

## Agent Usability

Measures how well the Skill guides an AI agent.

High score indicators:

- Required inputs are explicit
- Decision rules exist
- The workflow is deterministic
- Output structure is predictable

Low score indicators:

- Ambiguous instructions
- Missing decision logic
- Output format unclear

---

## Technical Depth

Evaluates the engineering value of the skill.

High score indicators:

- References real frameworks or tools
- Provides architectural guidance
- Defines implementation constraints
- Includes anti-pattern warnings

Low score indicators:

- Generic advice
- Theory without implementation detail
- No technical specificity

---

## Platform Realism

Measures how well the Skill reflects real runtime conditions.

High score indicators:

- Considers platform limitations
- Handles lifecycle events
- Handles concurrency
- Handles offline or unstable environments

Low score indicators:

- Ignores runtime behavior
- No lifecycle awareness
- Unrealistic assumptions

---

## Edge Case Coverage

Evaluates how well the Skill anticipates failure conditions.

High score indicators:

- Multiple realistic edge cases
- Concurrency issues addressed
- Recovery strategies implied

Low score indicators:

- Only happy-path scenarios
- No failure handling

---

## Output Contract Quality

Measures how structured and reviewable the Skill output is.

High score indicators:

- Output sections clearly defined
- Assumptions surfaced
- Includes verification checklist
- Ends with a concrete implementation step

Low score indicators:

- Vague outputs
- Missing verification
- No next-step guidance

---

# 3. Calculating the Final Score

The final score is the **average of all dimension scores**.

Example:

| Dimension | Score |
|----------|------|
| Structure | 9 |
| Agent Usability | 9 |
| Technical Depth | 8 |
| Platform Realism | 9 |
| Edge Case Coverage | 8 |
| Output Contract Quality | 9 |

Final Score:

```
(9 + 9 + 8 + 9 + 8 + 9) / 6 = 8.67
```

---

# 4. Acceptance Threshold

A Skill is considered **production ready** when:

- Final score ≥ **9**
- Structure score ≥ **9**
- Agent usability ≥ **9**
- Technical depth ≥ **8**

Skills scoring between **8 and 9** may be accepted with revisions.

Skills scoring **below 8** should be revised before merging.

---

# 5. Reviewer Guidance

Reviewers should prioritize:

1. clarity of decisions
2. deterministic workflows
3. real technical guidance
4. handling of failure conditions

Avoid rewarding verbosity or length alone.

High-quality skills are:

- precise
- structured
- actionable

---

# 6. Example Scorecard

```
Skill: mobile-auth-session-security

Structure: 9
Agent Usability: 9
Technical Depth: 8
Platform Realism: 9
Edge Case Coverage: 9
Output Contract Quality: 9

Final Score: 8.83
Status: Strong, minor improvements possible
```

---

# 7. Continuous Improvement

Scores should be revisited whenever:

- a Skill is significantly modified
- new edge cases are discovered
- framework behavior changes

Maintainers should update the scorecard to reflect improvements over time.
