# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Type

This is a **Claude Code skill repository**, not a traditional software project. It contains:
- A skill definition (`SKILL.md`) that teaches Claude Code to extract and preserve learned knowledge
- Templates for creating new skills (`resources/`)
- Example skills demonstrating proper format (`examples/`)
- Hook scripts for automatic activation (`scripts/`)

There are no build, test, or lint commands because this is documentation and configuration, not compiled code.

## Architecture

### Core Components

**Skill Definition (`SKILL.md`)**
- The main skill that Claude Code loads when activated
- Written in YAML frontmatter + markdown format
- Contains instructions for identifying, extracting, and structuring reusable knowledge
- Emphasizes web research before extraction to ensure current best practices

**Skill Template (`resources/skill-template.md`)**
- Standard structure for all extracted skills
- Required sections: Problem, Context/Trigger Conditions, Solution, Verification, Example, Notes
- YAML frontmatter with name, description, author, version, date

**Examples (`examples/`)**
- `nextjs-server-side-error-debugging/`: Server-side errors not appearing in browser console
- `prisma-connection-pool-exhaustion/`: Database connection issues in serverless
- `typescript-circular-dependency/`: Import cycle detection and resolution
- Each example demonstrates the complete skill format

**Activation Hook (`scripts/claudeception-activator.sh`)**
- Bash script that injects reminder text into Claude Code sessions
- Prompts evaluation after each user request for extractable knowledge
- Installed in `~/.claude/hooks/` and referenced in `~/.claude/settings.json`

### Skill File Format

All skills follow this structure:

```markdown
---
name: kebab-case-name
description: |
  Precise description for semantic matching. Must include:
  (1) exact use cases, (2) trigger conditions (error messages, symptoms),
  (3) what problem this solves
author: Claude Code
version: 1.0.0
date: YYYY-MM-DD
---

# [Skill Name]

## Problem
[What this solves]

## Context / Trigger Conditions
[Exact error messages, symptoms, scenarios]

## Solution
[Step-by-step instructions]

## Verification
[How to confirm it worked]

## Example
[Concrete before/after]

## Notes
[Caveats, edge cases, related considerations]

## References
[Optional: URLs to official docs, articles consulted during research]
```

### Critical Design Pattern: Description Field

The `description` field in YAML frontmatter determines when Claude Code surfaces a skill through semantic matching. Effective descriptions:
- Include exact error messages that trigger the skill
- Specify technologies/frameworks by name
- Use action phrases: "Use when...", "Helps with...", "Solves..."
- Are specific enough to match relevant contexts but general enough to catch variations

**Bad**: "Helps with database problems"
**Good**: "Fix for PrismaClientKnownRequestError: Too many database connections in serverless environments (Vercel, AWS Lambda). Use when connection count errors appear after ~5 concurrent requests."

## Installation Locations

Skills can be installed at two levels:

**User-level (recommended)**
- Path: `~/.claude/skills/[skill-name]/SKILL.md`
- Available in all projects for this user
- Used for general programming knowledge

**Project-level**
- Path: `.claude/skills/[skill-name]/SKILL.md`
- Only available in specific project
- Used for project-specific patterns

## Quality Criteria

Before extracting or modifying a skill, verify it meets these criteria:

1. **Reusable**: Will help with future tasks, not just this one instance
2. **Non-trivial**: Requires discovery through investigation, not just documentation lookup
3. **Specific**: Has clear trigger conditions (exact error messages, observable symptoms)
4. **Verified**: Solution has been tested and confirmed to work

## Research-First Approach

The skill emphasizes web research before extraction to ensure current best practices. When creating or updating skills:

**Always search for:**
- Official documentation for the technology/framework involved
- Current best practices (post-2025 preferred)
- Common patterns or solutions for similar problems
- Known gotchas or pitfalls

**Include a References section** with URLs to sources consulted, especially for technology-specific skills.

**Skip research for:**
- Project-specific internal patterns unique to this codebase
- Time-sensitive situations requiring immediate extraction

## Working with Skills in This Repository

### Modifying the Main Skill (`SKILL.md`)

The main skill's description field should be comprehensive enough to match multiple trigger phrases:
- `/claudeception` command
- "save this as a skill" or "extract a skill"
- Completion of non-obvious debugging or workarounds
- Trial-and-error discoveries

### Creating Example Skills

Example skills in `examples/` should:
- Demonstrate complete, realistic scenarios
- Include all required sections from the template
- Show both good trigger condition descriptions and poor ones (in commit history/docs)
- Have working code examples that can be verified

### Modifying the Activation Hook

The hook script (`scripts/claudeception-activator.sh`) outputs reminder text that Claude Code sees on every user prompt. Changes to this script affect:
- How aggressively the skill self-activates
- What evaluation criteria Claude uses to decide if knowledge is worth extracting
- The user experience (intrusive vs. subtle reminders)

Keep the reminder concise but clear about the non-negotiable evaluation requirement.

## Skill Lifecycle

Skills evolve through these stages:

1. **Creation**: Initial extraction with documented verification
2. **Refinement**: Updates based on additional use cases discovered
3. **Deprecation**: Mark when underlying tools/patterns change
4. **Archival**: Remove when no longer relevant

Version numbers and dates in YAML frontmatter track this lifecycle.

## Academic Foundation

The approach is based on research in skill libraries for AI agents:
- **Voyager** (Wang et al., 2023): Skill libraries help agents avoid re-learning
- **CASCADE** (2024): "Meta-skills" for acquiring skills
- **SEAgent** (2025): Learning environments through trial and error
- **Reflexion** (Shinn et al., 2023): Self-reflection improves learning

See README.md for full citations.
