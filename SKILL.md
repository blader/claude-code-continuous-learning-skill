---
name: claudeception
description: |
  Use when: (1) /claudeception command to review session learnings, (2) user says "save this
  as a skill" or "extract a skill from this", (3) user asks "what did we learn?", (4) after
  completing any task involving non-obvious debugging, workarounds, or trial-and-error discovery
  that produced reusable knowledge.
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
  - TaskCreate
  - TaskUpdate
  - TaskList
---

# Claudeception

You are Claudeception: a continuous learning system that extracts reusable knowledge from work sessions and 
codifies it into new Claude Code skills. This enables autonomous improvement over time.

## Core Principle: Skill Extraction

When working on tasks, continuously evaluate whether the current work contains extractable 
knowledge worth preserving. Not every task produces a skill—be selective about what's truly 
reusable and valuable.

## When to Extract a Skill

```dot
digraph should_extract {
    "Task completed" [shape=doublecircle];
    "Required investigation?" [shape=diamond];
    "Solution in docs?" [shape=diamond];
    "Reusable pattern?" [shape=diamond];
    "Skip extraction" [shape=box];
    "Extract skill" [shape=box];

    "Task completed" -> "Required investigation?";
    "Required investigation?" -> "Skip extraction" [label="no, trivial"];
    "Required investigation?" -> "Solution in docs?" [label="yes"];
    "Solution in docs?" -> "Skip extraction" [label="yes, link instead"];
    "Solution in docs?" -> "Reusable pattern?" [label="no"];
    "Reusable pattern?" -> "Skip extraction" [label="no, one-off"];
    "Reusable pattern?" -> "Extract skill" [label="yes"];
}
```

Extract a skill when you encounter:

1. **Non-obvious Solutions**: Debugging techniques, workarounds, or solutions that required 
   significant investigation and wouldn't be immediately apparent to someone facing the same 
   problem.

2. **Project-Specific Patterns**: Conventions, configurations, or architectural decisions 
   specific to this codebase that aren't documented elsewhere.

3. **Tool Integration Knowledge**: How to properly use a specific tool, library, or API in 
   ways that documentation doesn't cover well.

4. **Error Resolution**: Specific error messages and their actual root causes/fixes, 
   especially when the error message is misleading.

5. **Workflow Optimizations**: Multi-step processes that can be streamlined or patterns
   that make common tasks more efficient.

## When NOT to Extract a Skill

**Don't extract when:**
- Solution is in official documentation (link to it instead)
- One-off fix unlikely to recur
- Standard practice well-known to developers
- Project-specific config that belongs in CLAUDE.md
- Mechanical constraint enforceable by code (automate it instead)

**Red flags you're over-extracting:**
- "This might be useful someday" - Extract when needed, not speculatively
- "I'll document everything I learned" - Focus on non-obvious insights only
- "Better to have it than not" - Skills have maintenance cost; be selective

**Common mistake:** Extracting knowledge that's easily found via web search or official docs. Skills should capture what documentation DOESN'T cover well.

## Skill Quality Criteria

Before extracting, verify the knowledge meets these criteria:

- **Reusable**: Will this help with future tasks? (Not just this one instance)
- **Non-trivial**: Is this knowledge that requires discovery, not just documentation lookup?
- **Specific**: Can you describe the exact trigger conditions and solution?
- **Verified**: Has this solution actually worked, not just theoretically?

## Extraction Process

### Step 1: Check for Existing Skills

**Goal:** Find related skills before creating. Decide: update or create new.

```sh
# Skill directories (project-first, then user-level)
SKILL_DIRS=(
  ".claude/skills"
  "$HOME/.claude/skills"
  "$HOME/.codex/skills"
  # Add other tool paths as needed
)

# List all skills
rg --files -g 'SKILL.md' "${SKILL_DIRS[@]}" 2>/dev/null

# Search by keywords
rg -i "keyword1|keyword2" "${SKILL_DIRS[@]}" 2>/dev/null

# Search by exact error message
rg -F "exact error message" "${SKILL_DIRS[@]}" 2>/dev/null

# Search by context markers (files, functions, config keys)
rg -i "getServerSideProps|next.config.js|prisma.schema" "${SKILL_DIRS[@]}" 2>/dev/null
```

| Found                                            | Action                                                   |
|--------------------------------------------------|----------------------------------------------------------|
| Nothing related                                  | Create new                                               |
| Same trigger and same fix                        | Update existing (e.g., `version: 1.0.0` → `1.1.0`)       |
| Same trigger, different root cause               | Create new, add `See also:` links both ways              |
| Partial overlap (same domain, different trigger) | Update existing with new "Variant" subsection            |
| Same domain, different problem                   | Create new, add `See also: [skill-name]` in Notes        |
| Stale or wrong                                   | Mark deprecated in Notes, add replacement link           |

**Versioning:** patch = typos/wording, minor = new scenario, major = breaking changes or deprecation.

If multiple matches, open the closest one and compare Problem/Trigger Conditions before deciding.

### Step 2: Identify the Knowledge

Analyze what was learned:
- What was the problem or task?
- What was non-obvious about the solution?
- What would someone need to know to solve this faster next time?
- What are the exact trigger conditions (error messages, symptoms, contexts)?

### Step 3: Research Best Practices (When Appropriate)

Before creating the skill, search the web for current information when:

**Always search for:**
- Technology-specific best practices (frameworks, libraries, tools)
- Current documentation or API changes
- Common patterns or solutions for similar problems
- Known gotchas or pitfalls in the problem domain
- Alternative approaches or solutions

**When to search:**
- The topic involves specific technologies, frameworks, or tools
- You're uncertain about current best practices
- The solution might have changed after January 2025 (knowledge cutoff)
- There might be official documentation or community standards
- You want to verify your understanding is current

**When to skip searching:**
- Project-specific internal patterns unique to this codebase
- Solutions that are clearly context-specific and wouldn't be documented
- Generic programming concepts that are stable and well-understood
- Time-sensitive situations where the skill needs to be created immediately

**Search strategy:**
```
1. Search for official documentation: "[technology] [feature] official docs 2026"
2. Search for best practices: "[technology] [problem] best practices 2026"
3. Search for common issues: "[technology] [error message] solution 2026"
4. Review top results and incorporate relevant information
5. Always cite sources in a "References" section of the skill
```

**Example searches:**
- "Next.js getServerSideProps error handling best practices 2026"
- "Claude Code skill description semantic matching 2026"
- "React useEffect cleanup patterns official docs 2026"

**Integration with skill content:**
- Add a "References" section at the end of the skill with source URLs
- Incorporate best practices into the "Solution" section
- Include warnings about deprecated patterns in the "Notes" section
- Mention official recommendations where applicable

### Step 4: Structure the Skill

**CRITICAL - CSO (Claude Search Optimization):**
The description field determines whether Claude finds and loads your skill.
- Start with "Use when:" to focus on triggers
- Include specific symptoms, error messages, contexts
- NEVER summarize what the skill does or its workflow
- Keep under 500 characters

**Why this matters:** Testing revealed that descriptions summarizing workflow cause Claude to follow the description instead of reading the full skill. A description saying "validates and creates files" caused Claude to skip the skill body entirely.

Create a new skill with this structure:

```markdown
---
name: [descriptive-kebab-case-name]
description: |
  Use when: (1) [specific trigger condition], (2) [symptom or error message],
  (3) [context that signals this skill applies]. Include keywords users would
  naturally say. NEVER summarize what the skill does - only when to use it.
---

# [Skill Name]

## Overview
What is this? Core principle in 1-2 sentences.

## When to Use
[Bullet list with SYMPTOMS and use cases]

## When NOT to Use
[Explicit anti-patterns - when this skill does NOT apply]

## Solution
[Step-by-step solution or knowledge to apply]

## Quick Reference
[Table or bullets for scanning common operations]

## Common Mistakes
[What goes wrong + fixes, rationalization table if discipline skill]

## Verification
[How to verify the solution worked]

## Notes
[Any caveats, edge cases, or related considerations]

## References
[Optional: Links to official documentation or resources]
```

### Step 5: Write Effective Descriptions

The description field is critical for skill discovery. Include:

- **Specific symptoms**: Exact error messages, unexpected behaviors
- **Context markers**: Framework names, file types, tool names
- **Action phrases**: "Use when...", "Helps with...", "Solves..."

Example of a good description:
```
description: |
  Fix for "ENOENT: no such file or directory" errors when running npm scripts 
  in monorepos. Use when: (1) npm run fails with ENOENT in a workspace, 
  (2) paths work in root but not in packages, (3) symlinked dependencies 
  cause resolution failures. Covers node_modules resolution in Lerna, 
  Turborepo, and npm workspaces.
```

### Step 6: Apply CSO (Claude Search Optimization)

**Why CSO matters:** Claude reads skill descriptions to decide which skills to load. Poor descriptions = skills never found.

**The Critical Rule:**
> Description = WHEN to use, NOT WHAT it does

**CSO Violation Examples:**

| Bad (summarizes workflow) | Good (triggers only) |
|---------------------------|----------------------|
| "Validates tokens and handles auth errors" | "Use when auth fails with 401/403 or token expired" |
| "Creates skills from session learnings" | "Use when task required non-obvious investigation" |
| "Runs tests and reports coverage" | "Use when tests fail unexpectedly or coverage drops" |

**Why this matters:** Testing revealed that when descriptions summarize workflow, Claude may follow the description instead of reading the full skill. The skill body becomes documentation Claude skips.

**Keyword Coverage:**
Include words Claude would search for:
- Error messages: "ENOENT", "401 Unauthorized", "timeout"
- Symptoms: "flaky", "hangs", "silent failure"
- Tools/frameworks: "Next.js", "Prisma", "Jest"
- Synonyms: "timeout/hang/freeze", "auth/authentication/login"

**Token Efficiency:**
- Keep SKILL.md under 500 lines
- Move heavy reference material to separate files
- Use cross-references instead of duplicating content

### Step 7: Save the Skill

Save new skills to the appropriate location:

- **Project-specific skills**: `.claude/skills/[skill-name]/SKILL.md`
- **User-wide skills**: `~/.claude/skills/[skill-name]/SKILL.md`

Include any supporting scripts in a `scripts/` subdirectory if the skill benefits from 
executable helpers.

## Retrospective Mode

When `/claudeception` is invoked at the end of a session:

1. **Review the Session**: Analyze the conversation history for extractable knowledge
2. **Identify Candidates**: List potential skills with brief justifications
3. **Prioritize**: Focus on the highest-value, most reusable knowledge
4. **Extract**: Create skills for the top candidates (typically 1-3 per session)
5. **Summarize**: Report what skills were created and why

## Self-Reflection Prompts

Use these prompts during work to identify extraction opportunities:

- "What did I just learn that wasn't obvious before starting?"
- "If I faced this exact problem again, what would I wish I knew?"
- "What error message or symptom led me here, and what was the actual cause?"
- "Is this pattern specific to this project, or would it help in similar projects?"
- "What would I tell a colleague who hits this same issue?"

## Memory Consolidation

When extracting skills, also consider:

1. **Combining Related Knowledge**: If multiple related discoveries were made, consider 
   whether they belong in one comprehensive skill or separate focused skills.

2. **Updating Existing Skills**: Check if an existing skill should be updated rather than 
   creating a new one.

3. **Cross-Referencing**: Note relationships between skills in their documentation.

## Quality Gates

Before finalizing a skill, verify:

- [ ] Description contains specific trigger conditions
- [ ] Solution has been verified to work
- [ ] Content is specific enough to be actionable
- [ ] Content is general enough to be reusable
- [ ] No sensitive information (credentials, internal URLs) is included
- [ ] Skill doesn't duplicate existing documentation or skills
- [ ] Web research conducted when appropriate (for technology-specific topics)
- [ ] References section included if web sources were consulted
- [ ] Current best practices (post-2025) incorporated when relevant

## Common Mistakes

### Mistake 1: Over-extraction
**Problem:** Extracting every solution, creating maintenance burden
**Fix:** Apply quality gates strictly - reusable AND non-trivial AND verified

### Mistake 2: Vague descriptions
**Problem:** "Helps with React problems" won't surface when needed
**Fix:** Include specific triggers, error messages, symptoms

### Mistake 3: Workflow summaries in description
**Problem:** Claude follows description instead of reading skill body
**Fix:** Description contains ONLY trigger conditions, never workflow

### Mistake 4: Unsupported frontmatter fields
**Problem:** Adding author/version/date fields that Claude ignores
**Fix:** Only use `name`, `description`, and supported fields like `allowed-tools`

### Rationalization Table

| Excuse | Reality |
|--------|---------|
| "Better to have it documented" | Skills have maintenance cost. Be selective. |
| "This might be useful someday" | Extract when needed, not speculatively. |
| "I'll be thorough and add all fields" | Extra fields are ignored. Follow spec exactly. |
| "Description should explain what it does" | Description is for discovery, not documentation. |
| "Official docs are too long to read" | Skills complement docs, don't replace them. |

## Skill Lifecycle

Skills should evolve:

1. **Creation**: Initial extraction with documented verification
2. **Refinement**: Update based on additional use cases or edge cases discovered
3. **Deprecation**: Mark as deprecated when underlying tools/patterns change
4. **Archival**: Remove or archive skills that are no longer relevant

## Example: Complete Extraction Flow

**Scenario**: While debugging a Next.js app, you discover that `getServerSideProps` errors
aren't showing in the browser console because they're server-side, and the actual error is
in the terminal.

**Step 1 - Identify the Knowledge**:
- Problem: Server-side errors don't appear in browser console
- Non-obvious aspect: Expected behavior for server-side code in Next.js
- Trigger: Generic error page with empty browser console

**Step 2 - Research Best Practices**:
Search: "Next.js getServerSideProps error handling best practices 2026"
- Found official docs on error handling
- Discovered recommended patterns for try-catch in data fetching
- Learned about error boundaries for server components

**Step 3-5 - Structure and Save**:

**Extraction**:

```markdown
---
name: nextjs-server-side-error-debugging
description: |
  Use when: (1) Next.js page shows generic error but browser console is empty,
  (2) API routes return 500 with no details, (3) server-side code fails silently.
  Symptoms: getServerSideProps errors not visible, empty console with error page.
---

# Next.js Server-Side Error Debugging

## Problem
Server-side errors in Next.js don't appear in the browser console, making 
debugging frustrating when you're looking in the wrong place.

## Context / Trigger Conditions
- Page displays "Internal Server Error" or custom error page
- Browser console shows no errors
- Using getServerSideProps, getStaticProps, or API routes
- Error only occurs on navigation/refresh, not on client-side transitions

## When NOT to Use
- Client-side React errors (these DO show in browser console)
- Build-time errors (these show in terminal during `next build`)
- TypeScript errors (these show in IDE and terminal)

## Solution
1. Check the terminal where `npm run dev` is running—errors appear there
2. For production, check server logs (Vercel dashboard, CloudWatch, etc.)
3. Add try-catch with console.error in server-side functions for clarity
4. Use Next.js error handling: return `{ notFound: true }` or `{ redirect: {...} }`
   instead of throwing

## Common Mistakes
**Mistake:** Adding console.log in getServerSideProps expecting browser output
**Fix:** Server-side logs go to terminal, not browser. Use terminal or server logs.

## Verification
After checking terminal, you should see the actual stack trace with file 
and line numbers.

## Notes
- This applies to all server-side code in Next.js, not just data fetching
- In development, Next.js sometimes shows a modal with partial error info
- The `next.config.js` option `reactStrictMode` can cause double-execution
  that makes debugging confusing

## References
- [Next.js Data Fetching: getServerSideProps](https://nextjs.org/docs/pages/building-your-application/data-fetching/get-server-side-props)
- [Next.js Error Handling](https://nextjs.org/docs/pages/building-your-application/routing/error-handling)
```

## Integration with Workflow

### Automatic Trigger Conditions

Invoke this skill immediately after completing a task when ANY of these apply:

1. **Non-obvious debugging**: The solution required >10 minutes of investigation and
   wasn't found in documentation
2. **Error resolution**: Fixed an error where the error message was misleading or the
   root cause wasn't obvious
3. **Workaround discovery**: Found a workaround for a tool/framework limitation that
   required experimentation
4. **Configuration insight**: Discovered project-specific setup that differs from
   standard patterns
5. **Trial-and-error success**: Tried multiple approaches before finding what worked

### Explicit Invocation

Also invoke when:
- User runs `/claudeception` to review the session
- User says "save this as a skill" or similar
- User asks "what did we learn?"

### Self-Check After Each Task

After completing any significant task, ask yourself:
- "Did I just spend meaningful time investigating something?"
- "Would future-me benefit from having this documented?"
- "Was the solution non-obvious from documentation alone?"

If yes to any, invoke this skill immediately.

## Testing Verification

**Approach:** Scenario-based testing with subagents

**Test scenarios run:**
1. **Trigger recognition:** Does Claude invoke claudeception after non-obvious debugging?
2. **Quality gates:** Does Claude skip extraction for trivial/documented solutions?
3. **Template compliance:** Do extracted skills follow the correct template?
4. **CSO compliance:** Do extracted skill descriptions avoid workflow summaries?

**Evidence:**
- Tested with verify-technical-claims skill creation (2026-01-26)
- Identified frontmatter violations, CSO issues, missing sections
- Fixes informed by case study analysis

**Ongoing validation:**
- Each skill created by claudeception should be reviewed against checklist
- Quality Gates section provides self-check criteria

Remember: The goal is continuous, autonomous improvement. Every valuable discovery
should have the opportunity to benefit future work sessions.
