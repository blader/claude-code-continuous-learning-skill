# Research Workflow

Detailed guidance for conducting web research before creating skills.

## When to Search

**Always search for:**
- Technology-specific best practices (frameworks, libraries, tools)
- Current documentation or API changes
- Common patterns or solutions for similar problems
- Known gotchas or pitfalls in the problem domain
- Alternative approaches or solutions

**When to search:**
- The topic involves specific technologies, frameworks, or tools
- Uncertainty about current best practices
- The solution might have changed after knowledge cutoff
- There might be official documentation or community standards
- Verification of understanding is needed

**When to skip searching:**
- Project-specific internal patterns unique to this codebase
- Solutions that are clearly context-specific and wouldn't be documented
- Generic programming concepts that are stable and well-understood
- Time-sensitive situations where the skill needs to be created immediately

## Search Strategy

```
1. Search for official documentation: "[technology] [feature] official docs [year]"
2. Search for best practices: "[technology] [problem] best practices [year]"
3. Search for common issues: "[technology] [error message] solution [year]"
4. Review top results and incorporate relevant information
5. Always cite sources in a "References" section of the skill
```

## Example Searches

- "Next.js getServerSideProps error handling best practices 2026"
- "Claude Code skill description semantic matching 2026"
- "React useEffect cleanup patterns official docs 2026"
- "Prisma connection pool serverless best practices 2026"

## Integrating Research into Skills

### References Section

Add a "References" section at the end of the skill with source URLs:

```markdown
## References

- [Official Documentation Title](https://example.com/docs)
- [Helpful Blog Post](https://example.com/blog/post)
- [Stack Overflow Answer](https://stackoverflow.com/questions/...)
```

### Best Practices Integration

- Incorporate best practices into the "Solution" section
- Include warnings about deprecated patterns in the "Notes" section
- Mention official recommendations where applicable
- Note when community practices differ from official guidance

### Citation Format

For academic references:

```
@misc{skill-name,
  title={Skill Title},
  author={Claude Code},
  year={2026},
  note={Based on [technology] documentation and community best practices}
}
```

## Quality Indicators

Good research produces skills that:

- Reference current (not outdated) documentation
- Include official recommendations where available
- Note version-specific caveats
- Distinguish between official guidance and community practices
