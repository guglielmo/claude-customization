# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a personal workspace for developing custom Claude Code skills. Each skill gets its own directory for the complete development process including planning, reasoning, and context gathering. The final output is a markdown document deployed to `~/.claude/skills/`.

**Note**: This workspace USES the superpowers plugin's `writing-skills` skill as a development tool, but the skills created here are your personal skills, not part of the superpowers plugin.

## Skill Development Workflow

**REQUIRED**: Always use the `superpowers:writing-skills` skill when creating new skills. This applies TDD methodology to documentation.

### Complete Development Process

1. **Create a directory** for the new skill (e.g., `skill-name/`)
2. **Planning phase** (optional): Create design document in `docs/plans/` if complex
3. **RED Phase - Baseline Testing**:
   - Create `test-scenarios.md` with pressure scenarios
   - Dispatch subagents WITHOUT the skill to document baseline behavior
   - Identify rationalizations and failure patterns in `baseline-results.md`
4. **GREEN Phase - Write Minimal Skill**:
   - Write `SKILL.md` addressing specific baseline failures
   - Test with subagents to verify compliance
   - Document results in `green-phase-results.md`
5. **REFACTOR Phase - Close Loopholes**:
   - Find new rationalizations
   - Add explicit counters
   - Re-test until bulletproof
6. **Quality Checks**: Verify against `quality-checklist.md`
7. **Deploy**: Copy `SKILL.md` to `~/.claude/skills/<skill-name>.md`

### Key Principles

- **Never write skill before testing**: Run baseline scenarios first (RED phase)
- **Test with pressure**: Combine urgency, sunk cost, exhaustion for discipline skills
- **Document verbatim**: Capture exact agent rationalizations
- **One skill at a time**: Complete full TDD cycle before moving to next skill

## Tools Used

**superpowers:writing-skills**: Development methodology for creating skills with TDD approach
- Guides RED-GREEN-REFACTOR cycle for documentation
- Ensures skills are tested with subagents before deployment
- Provides quality checklist and best practices

This is a tool used DURING development, not the destination for completed skills.

## Directory Structure

```
skill-name/
  SKILL.md                    # Final skill (deploy to ~/.claude/skills/)
  test-scenarios.md           # Pressure scenarios for testing
  baseline-results.md         # Agent behavior WITHOUT skill
  green-phase-results.md      # Agent behavior WITH skill
  quality-checklist.md        # TDD checklist verification
```

## Skill File Format (SKILL.md)

**Frontmatter** (YAML):
```yaml
---
name: skill-name-with-hyphens
description: Use when [specific triggers] - [what it does, third person, under 500 chars]
---
```

**Sections**:
- Overview (core principle in 1-2 sentences)
- When to Use (triggers, symptoms, use cases)
- Workflow/Pattern (the actual technique)
- Red Flags / Rationalizations (for discipline skills)
- Error Handling / Edge Cases
- Common Mistakes (with fixes)

**Quality Requirements**:
- Name: letters, numbers, hyphens only (no parentheses)
- Description: starts with "Use when...", third person
- Frontmatter: max 1024 characters total
- Word count: <500 for frequently-loaded, <1000 for workflow skills
- Keywords throughout for search optimization
