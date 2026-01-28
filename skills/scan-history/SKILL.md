---
name: scan-history
description: |
  Retrospectively scan Claude Code conversation history to extract learnings and create skills.
  Use when: (1) "/scan-history" command to analyze past sessions, (2) "extract skills from history",
  (3) "review past conversations for learnings". Analyzes JSONL conversation files from the current
  project, identifies non-obvious solutions and debugging patterns, presents candidates for
  confirmation, then creates skill files. Supports --user flag to save skills at user level.
author: Claude Code
version: 1.0.0
date: 2026-01-28
allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - Bash
  - AskUserQuestion
  - TodoWrite
---

# Scan History

Retrospectively analyze Claude Code conversation history to extract learnings and codify them as reusable skills.

## Overview

This skill scans past conversation sessions for the current project, identifies valuable knowledge that wasn't captured as skills, and presents candidates for user confirmation before creating them.

## Arguments

- `--user`: Save extracted skills to `~/.claude/skills/` (user-level) instead of `.claude/skills/` (project-level)

## Process

### Step 1: Locate Conversation History

Determine the project's conversation history location:

```bash
# Get current working directory
CWD=$(pwd)

# Convert to Claude's project path format (replace / with -)
PROJECT_PATH=$(echo "$CWD" | sed 's|^/|-|' | sed 's|/|-|g')

# History directory
HISTORY_DIR="$HOME/.claude/projects/$PROJECT_PATH"

echo "Looking for conversations in: $HISTORY_DIR"
ls -la "$HISTORY_DIR"/*.jsonl 2>/dev/null | head -20
```

### Step 2: Analyze Conversations

For each conversation file found:

1. **Read the JSONL file** and parse message entries
2. **Extract user/assistant exchanges** - focus on `type: "user"` and `type: "assistant"` entries
3. **Identify learning patterns**:
   - Debugging sessions (multiple attempts, error messages, eventual resolution)
   - Non-obvious discoveries (solutions that weren't immediately apparent)
   - Workarounds (creative solutions to tool/framework limitations)
   - Configuration insights (project-specific setup patterns)
   - Error resolutions (misleading errors with actual root causes)

**Signals of extractable knowledge:**
- Conversation contains error messages that were eventually resolved
- Multiple approaches tried before success
- User expressed confusion that was later clarified
- Solution required investigation beyond documentation
- Conversation length suggests non-trivial problem-solving

**Skip conversations that are:**
- Simple file reads or edits without problem-solving
- Routine tasks with no investigation
- Incomplete or abandoned sessions
- Already captured in existing skills

### Step 3: Score and Rank Candidates

For each potential learning, evaluate:

| Criterion | Weight | Question |
|-----------|--------|----------|
| Reusability | High | Would this help in future similar situations? |
| Non-triviality | High | Did this require discovery, not just docs? |
| Specificity | Medium | Are trigger conditions clear and searchable? |
| Verification | Medium | Was the solution confirmed to work? |
| Uniqueness | Low | Does an existing skill already cover this? |

Assign confidence scores:
- **High**: Clear problem, verified solution, obviously reusable
- **Medium**: Good learning but may be too specific or partially verified
- **Low**: Potentially useful but needs refinement or verification

### Step 4: Present Candidates

Display a summary table of all identified candidates:

```
## Extracted Learning Candidates

| # | Date | Session | Proposed Skill Name | Description | Confidence |
|---|------|---------|---------------------|-------------|------------|
| 1 | 2026-01-15 | abc123 | go-integration-test-setup | Setting up integration tests with real database connections | High |
| 2 | 2026-01-20 | def456 | slack-api-oauth-token-refresh | Handling OAuth token refresh for Slack API in tests | Medium |
| 3 | 2026-01-25 | ghi789 | entity-tree-test-isolation | Creating isolated test entities with proper cleanup | High |

### Candidate Details

#### 1. go-integration-test-setup (High Confidence)

**Problem:** Integration tests failing due to database connection issues
**Solution:** Use test helpers to manage config and database connections
**Trigger:** "connection refused" or "too many connections" in Go tests
**Source conversation:** 2026-01-15, session abc123

---
```

Use AskUserQuestion to let the user select which candidates to create:

```
Which candidates would you like to create as skills?
[ ] 1. go-integration-test-setup (High)
[ ] 2. slack-api-oauth-token-refresh (Medium)
[ ] 3. entity-tree-test-isolation (High)
```

### Step 5: Create Skills

For each confirmed candidate:

1. **Check for existing skills** (as per claudeception Step 1)
2. **Research best practices** if applicable (as per claudeception Step 3)
3. **Generate skill content** using the standard template
4. **Determine save location**:
   - Default: `.claude/skills/<skill-name>/SKILL.md`
   - With `--user`: `~/.claude/skills/<skill-name>/SKILL.md`
5. **Write the skill file**
6. **Report creation**

### Step 6: Summary

After creating all confirmed skills, provide a summary:

```
## Skills Created

| Skill | Location | Status |
|-------|----------|--------|
| go-integration-test-setup | .claude/skills/go-integration-test-setup/SKILL.md | Created |
| entity-tree-test-isolation | .claude/skills/entity-tree-test-isolation/SKILL.md | Created |

Next steps:
- Review the created skills and refine descriptions if needed
- Test that skills surface correctly when relevant context appears
```

## Quality Gates

Before presenting a candidate, verify:

- [ ] Clear problem statement can be extracted
- [ ] Solution is specific and actionable
- [ ] Trigger conditions can be identified
- [ ] Not already covered by existing skills
- [ ] No sensitive information (credentials, internal URLs)

## Example Usage

```
User: /scan-history

Claude: Looking for conversations in ~/.claude/projects/-Users-username-Development-myproject/

Found 15 conversation files. Analyzing...

## Extracted Learning Candidates

| # | Date | Proposed Skill | Description | Confidence |
|---|------|----------------|-------------|------------|
| 1 | 2026-01-10 | prisma-migration-rollback | Rolling back failed Prisma migrations safely | High |
| 2 | 2026-01-18 | nextjs-middleware-debugging | Debugging Next.js middleware execution order | Medium |

Which candidates would you like to create as skills?
```

```
User: /scan-history --user

Claude: [Same analysis, but skills will be saved to ~/.claude/skills/]
```

## Notes

- Conversation files are JSONL format with entries like: `{"type": "user", "message": {...}}`
- Large conversations may be summarized rather than fully analyzed
- Skills created should follow the standard template from `resources/skill-template.md`
- This skill complements real-time extraction (claudeception) by catching missed learnings
- Consider running periodically (weekly/monthly) to capture accumulated knowledge

## Limitations

- Cannot analyze conversations from before Claude Code started storing history
- Very long conversations may hit token limits and require chunked analysis
- Confidence scoring is heuristic and may miss valuable learnings or flag false positives
- Cannot verify solutions still work if codebase has changed significantly
