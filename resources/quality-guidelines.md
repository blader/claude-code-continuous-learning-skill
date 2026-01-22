# Quality Guidelines

Comprehensive guidance for maintaining skill quality throughout the lifecycle.

## Quality Gates

Before finalizing a skill, verify:

### Content Quality
- [ ] Description contains specific trigger conditions
- [ ] Solution has been verified to work
- [ ] Content is specific enough to be actionable
- [ ] Content is general enough to be reusable
- [ ] No sensitive information (credentials, internal URLs) included

### Research Quality
- [ ] Web research conducted when appropriate (for technology-specific topics)
- [ ] References section included if web sources were consulted
- [ ] Current best practices incorporated when relevant
- [ ] Skill doesn't duplicate existing documentation

### Structure Quality
- [ ] Name is descriptive and uses kebab-case
- [ ] Description enables semantic matching
- [ ] All template sections completed
- [ ] Code examples are complete and tested
- [ ] Verification steps are included

## Anti-Patterns to Avoid

### Over-extraction
Not every task deserves a skill. Mundane solutions don't need preservation.

**Signs of over-extraction:**
- The solution is in official documentation
- It took <5 minutes to figure out
- It's a one-off fix unlikely to recur

### Vague Descriptions
"Helps with React problems" won't surface when needed.

**Bad:** `description: Helps with database issues`
**Good:** `description: Fix for "Connection pool exhausted" in Prisma with serverless. Use when: (1) queries timeout after cold start...`

### Unverified Solutions
Only extract what actually worked.

**Before extracting, confirm:**
- The solution resolved the original problem
- No new issues were introduced
- The fix is reproducible

### Documentation Duplication
Don't recreate official docs; link to them and add what's missing.

**Instead of copying docs:**
- Link to official documentation
- Add context about when the documented solution doesn't work
- Include gotchas not mentioned in docs

### Stale Knowledge
Mark skills with versions and dates; knowledge becomes outdated.

**Include in every skill:**
- `version: 1.0.0` in frontmatter
- `date: YYYY-MM-DD` of creation
- Technology versions in Notes section

## Skill Lifecycle

### 1. Creation
Initial extraction with documented verification.

**Checklist:**
- Problem clearly identified
- Solution verified working
- Trigger conditions documented
- Quality gates passed

### 2. Refinement
Update based on additional use cases or edge cases discovered.

**When to refine:**
- New edge cases discovered
- Better solutions found
- Related problems identified
- User feedback received

### 3. Deprecation
Mark as deprecated when underlying tools/patterns change.

**Deprecation notice format:**
```markdown
> **DEPRECATED (YYYY-MM-DD)**: This skill applies to [tool] v1.x.
> For v2.x and later, see `[new-skill-name]`.
```

### 4. Archival
Remove or archive skills that are no longer relevant.

**Archive when:**
- Technology is no longer used
- Better alternatives exist
- Problem no longer occurs in modern versions

## Self-Reflection Prompts

Use these during work to identify extraction opportunities:

- "What did I just learn that wasn't obvious before starting?"
- "If I faced this exact problem again, what would I wish I knew?"
- "What error message or symptom led me here, and what was the actual cause?"
- "Is this pattern specific to this project, or would it help in similar projects?"
- "What would I tell a colleague who hits this same issue?"

## Memory Consolidation

When extracting skills, consider:

### Combining Related Knowledge
If multiple related discoveries were made, evaluate whether they belong in:
- One comprehensive skill (if tightly coupled)
- Separate focused skills (if independently useful)

### Updating Existing Skills
Check if an existing skill should be updated rather than creating a new one:
- Search existing skills for related topics
- Prefer updating over duplicating
- Note relationships between skills

### Cross-Referencing
Document relationships between skills:
```markdown
## Related Skills
- `related-skill-name` — Handles the X case
- `another-skill` — Alternative approach for Y
```
