---
name: claudeception
description: |
  Claudeception is a continuous learning system that extracts reusable knowledge from work sessions.
  Always asks for user confirmation before creating any skill. Triggers: (1) /claudeception command
  to review session learnings, (2) "save this as a skill" or "extract a skill from this",
  (3) "what did we learn?", (4) After any task involving non-obvious debugging, workarounds,
  or trial-and-error discovery. Identifies valuable knowledge and requests approval before saving.
author: Claude Code
version: 4.1.0
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - WebSearch
  - WebFetch
  - Skill
  - AskUserQuestion
  - TodoWrite
---

# Claudeception

A continuous learning system that extracts reusable knowledge from work sessions and codifies it into Claude Code skills, enabling autonomous improvement over time.

## Core Principle

Continuously evaluate whether current work contains extractable knowledge worth preserving. Be selective—not every task produces a skill.

## User Confirmation Required

**IMPORTANT**: Never create a skill without explicit user approval. Before writing any skill file:

1. Identify the extractable knowledge
2. Present a brief summary to the user:
   - What knowledge was identified
   - Proposed skill name
   - Why it's worth preserving
3. Ask: "Would you like me to create a skill for this?"
4. Only proceed if the user confirms

This ensures users maintain control over their skill library and prevents unwanted skill creation.

## When to Extract

Extract a skill when encountering:

| Type | Description |
|------|-------------|
| **Non-obvious Solutions** | Debugging techniques or workarounds requiring significant investigation |
| **Project Patterns** | Codebase-specific conventions not documented elsewhere |
| **Tool Integration** | Undocumented ways to use tools, libraries, or APIs |
| **Error Resolution** | Misleading error messages with non-obvious root causes |
| **Workflow Optimizations** | Multi-step processes that can be streamlined |

## Quality Criteria

Before extracting, verify the knowledge is:

- **Reusable** — Helps future tasks, not just this instance
- **Non-trivial** — Requires discovery, not documentation lookup
- **Specific** — Has exact trigger conditions and solution
- **Verified** — Actually worked, not theoretical

## Extraction Process

### Step 1: Identify the Knowledge

Analyze what was learned:
- What was the problem?
- What was non-obvious about the solution?
- What are the exact trigger conditions (error messages, symptoms)?

### Step 2: Research Best Practices

For technology-specific topics, search the web for current best practices before creating the skill. See `resources/research-workflow.md` for detailed search strategies.

Skip searching for project-specific internal patterns or stable programming concepts.

### Step 3: Structure the Skill

Use the template in `resources/skill-template.md`. Key sections:

```markdown
---
name: descriptive-kebab-case-name
description: |
  [Precise description with: (1) exact use cases, (2) trigger conditions
  like error messages, (3) what problem this solves]
author: Claude Code
version: 1.0.0
date: YYYY-MM-DD
---

# Skill Name

## Problem
## Context / Trigger Conditions
## Solution
## Verification
## Notes
## References
```

### Step 4: Write Effective Descriptions

The description enables semantic discovery. Include:

- **Specific symptoms**: Exact error messages, behaviors
- **Context markers**: Framework names, file types, tools
- **Action phrases**: "Use when...", "Helps with...", "Solves..."

**Good example:**
```yaml
description: |
  Fix for "ENOENT: no such file or directory" in monorepo npm scripts.
  Use when: (1) npm run fails with ENOENT in workspace, (2) paths work
  in root but not packages. Covers Lerna, Turborepo, npm workspaces.
```

### Step 5: Request User Confirmation

Before creating the skill, present a summary and ask for approval:

```
I identified extractable knowledge:

**Topic**: [brief description]
**Proposed skill**: `[skill-name]`
**Why**: [1-2 sentence justification]

Would you like me to create this skill?
```

**Only proceed if the user confirms.** If declined, do not create the skill.

### Step 6: Save the Skill

After user approval:

- **Project-specific**: `.claude/skills/[skill-name]/SKILL.md`
- **User-wide**: `~/.claude/skills/[skill-name]/SKILL.md`

Include supporting scripts in a `scripts/` subdirectory when helpful.

## Retrospective Mode

When `/claudeception` is invoked:

1. **Review** — Analyze conversation history for extractable knowledge
2. **Identify** — List potential skills with brief justifications
3. **Prioritize** — Focus on highest-value, most reusable knowledge
4. **Confirm** — Present candidates and ask which ones to create
5. **Extract** — Create only the skills the user approved
6. **Summarize** — Report what was created and why

## Automatic Triggers

Invoke this skill immediately after completing a task when ANY apply:

- Solution required >10 minutes of investigation not found in docs
- Fixed an error where the message was misleading
- Found a workaround requiring experimentation
- Discovered project-specific setup differing from standard patterns
- Tried multiple approaches before finding what worked

## Explicit Invocation

Also invoke when:
- User runs `/claudeception`
- User says "save this as a skill" or similar
- User asks "what did we learn?"

## Self-Check

After completing significant tasks, ask:
- "Did I spend meaningful time investigating something?"
- "Would future-me benefit from this documented?"
- "Was the solution non-obvious from documentation alone?"

If yes to any, invoke this skill immediately.

## Additional Resources

### Reference Files

Consult these for detailed guidance:

| File | Contents |
|------|----------|
| `resources/skill-template.md` | Complete skill template with checklist |
| `resources/research-workflow.md` | Web research strategies and citation format |
| `resources/quality-guidelines.md` | Quality gates, anti-patterns, skill lifecycle |
| `resources/research-references.md` | Academic background (Voyager, CASCADE, etc.) |

### Working Examples

Real extracted skills in `examples/`:

- `nextjs-server-side-error-debugging/` — Server-side error debugging
- `prisma-connection-pool-exhaustion/` — Database connection issues
- `typescript-circular-dependency/` — Import cycle resolution

Study these as models for effective skill structure.
