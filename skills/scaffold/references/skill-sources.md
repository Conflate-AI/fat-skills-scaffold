# Skill Sources Reference

How to discover and install skills from various sources.

## skills.sh marketplace

The primary skill discovery mechanism. Uses the `skills` CLI (`npx skills`).

### Searching

```bash
npx skills search "<query>"
```

- Use 2-4 varied keyword angles per domain for broad coverage
- Results include install counts — always use these live counts rather than any cached values
- Results include a `skills.sh/<owner>/<repo>/<skill>` link for each skill — surface these to the user for review

Search keyword strategies per domain:

| Domain | Search queries |
|---|---|
| Engineering | "tdd", "code review", "debugging", "architecture" |
| Finance | "financial analysis", "stock market", "investment", "valuation" |
| Branding | "personal branding", "content creation", "social media", "seo" |
| GTM | "go to market", "launch strategy", "competitive analysis", "positioning" |
| Career | "career development", "resume", "interview prep", "job search" |
| Research | "research", "literature review", "knowledge management", "synthesis" |
| Product | "product management", "prd", "user stories", "roadmap" |

For "Other" domains, derive keywords from the user's description. Use broad terms first, then narrow.

### Installing

```bash
cd <project_directory> && npx skills add <owner/repo@skill> --yes
```

- Always use `--yes` to skip interactive prompts (the user already approved in Phase 2)
- This installs to `.agents/skills/` (universal) and symlinks to harness-specific directories
- Idempotent — re-running with the same skill is safe (skips if already installed)

### Handling failures

If `npx skills search` fails:
- Inform the user: "skills.sh appears to be unavailable. I'll proceed with catalog recommendations only. Re-run `/scaffold` later for full marketplace coverage."
- Continue with catalog skills only — don't block the entire setup

If `npx skills add` fails for a specific skill:
- Note the failure and continue with remaining skills
- Suggest the user manually install it later: `npx skills add <owner/repo@skill>`

## Known skill repositories

These are curated repos with high-quality skills, organized by domain:

### Engineering

| Repo | Skills | Quality |
|---|---|---|
| [mattpocock/skills](https://github.com/mattpocock/skills) | tdd, grill-with-docs, diagnosing-bugs, to-prd, implement, improve-codebase-architecture, triage, to-issues, codebase-design, grill-me, handoff, writing-great-skills | Very high — 220K+ installs on skills.sh |
| [tech4ge/Agent-Scaffold](https://github.com/tech4ge/Agent-Scaffold) | tdd, diagnose, triage, to-prd, grill-with-docs, review, improve-codebase-architecture, zoom-out, setup-agent-skills, to-issues, kanban-based-development, caveman | High — proven in production at 4ge |
| [anthropics/skills](https://github.com/anthropics/skills) | docx, pdf, pptx, xlsx, web development | High — official Anthropic skills |

### Productivity & Writing

| Repo | Skills | Quality |
|---|---|---|
| [mattpocock/skills](https://github.com/mattpocock/skills) | grill-me, grilling, handoff, teach, writing-beats, writing-fragments, writing-shape, edit-article, obsidian-vault | High |

### Domain-specific (lower install counts, review before recommending)

Search the marketplace for domain-specific skills. Always check install counts and review the skill on skills.sh before including in the plan.

## Web search fallback

For custom domains or sparse marketplace coverage, use `web_search` with queries like:

- `"best agent skills for <domain>"`
- `"MCP servers for <domain> workflows"`
- `"<domain> AI agent tools and extensions"`
- `"claude code skills <domain>"`

Web search results may surface:
- Skills not yet on skills.sh (direct GitHub repos)
- MCP servers for the domain
- Blog posts recommending specific tools
- Workflow patterns that should become custom skills
