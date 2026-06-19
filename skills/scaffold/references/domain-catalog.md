# Domain Catalog Reference

The catalog maps each domain to recommended skills, extensions, and custom skill candidates. Catalogs live in `catalog/<domain>.yaml` relative to the skill root.

## Reading a catalog

Each catalog file has this structure:

```yaml
domain: <domain-name>
description: <what this domain covers>

skills:
  <source-name>:           # e.g. "mattpocock", "marketplace", "tech4ge"
    - name: <skill-name>   # human-readable name
      skill: <owner/repo@skill>  # skills.sh identifier
      description: <what it does>
      tags: [tag1, tag2]
      condition: "<optional condition>"  # e.g. "tech_stack includes typescript"
      recommended: true/false

  custom:                   # skills to generate for gaps
    - name: <skill-name>
      description: <what it should do>
      tags: [tag1, tag2]
      interview_questions:
        - <question 1>
        - <question 2>

extensions:
  pi:                       # Pi-specific extensions
    required:
      - name: <extension-name>
        source: <pi-install-source>
        description: <what it adds>
    recommended:
      - name: <extension-name>
        source: <pi-install-source>
        description: <what it adds>

  claude-code:              # Claude Code MCP servers
    mcp-servers:
      - name: <server-name>
        config: { command: "...", args: [...] }
        description: <what it provides>

  cursor:                   # Cursor MCP servers
    mcp-servers:
      - name: <server-name>
        config: { command: "...", args: [...] }
```

## Using the catalog in Phase 2

1. **Match domain** — use the user's selected domain to find the right catalog file
2. **Filter by conditions** — skip skills whose `condition` doesn't match the user's context (e.g. skip `tushare-finance` if user isn't working with Chinese markets)
3. **Merge with live search** — catalog skills are the baseline; live `npx skills search` results may add new skills or update install counts
4. **Present all together** — show catalog + marketplace results in a single unified list, sorted by install count (descending), with catalog recommendations pre-checked

## Adding new domains

To add a new domain, create `catalog/<domain>.yaml` following the structure above. The "Other" domain in the interview has no catalog — it relies entirely on live search + web search + custom skill generation.

## Sources

Known high-quality skill sources:

| Source | Identifier | Focus |
|---|---|---|
| Matt Pocock | `mattpocock/skills@<skill>` | Engineering workflow, writing, productivity |
| tech4ge Agent-Scaffold | `git:github.com/tech4ge/Agent-Scaffold` | Full engineering lifecycle (16 skills) |
| Vercel | `vercel-labs/skills` | React, Next.js, frontend best practices |
| Anthropic | `anthropics/skills` | Document processing, web dev |
| Pi Skills (badlogic) | `badlogic/pi-skills` | Web search, browser automation, Google APIs |
