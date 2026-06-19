# fat-skills-scaffold

Goal-driven agent setup bootstrapper. State your goal, answer a short interview, and get a complete agent environment with curated skills, tailored AGENTS.md, process state machines, and harness-specific extensions.

Works with **Pi, Claude Code, Cursor, Codex, Gemini CLI**, and any harness that reads from `.agents/skills/` or `AGENTS.md`.

## Quick start

```bash
# Install as a Pi package
pi install git:github.com/Conflate-AI/fat-skills-scaffold

# In any project directory, run the bootstrap skill
/bootstrap
```

The skill walks you through a 4-phase workflow:

1. **Goal discovery** — structured interview about what you're building, which harnesses you use, team size, communication style
2. **Research & planning** — catalog lookup, live marketplace search, gap analysis, plan presented for your approval (with install counts, links to review, and override toggles)
3. **Generate & install** — skills installed via `npx skills add`, AGENTS.md generated, extensions configured, custom skills created for gaps
4. **Verify & handoff** — everything validated, summary of what was created, next steps suggested

Run `/bootstrap` again to add another domain. Skills are idempotent, AGENTS.md is append-only, state machines compose.

## How it works

### Skills go to `.agents/skills/` (universal)

All skills install via `npx skills add`, which writes to `.agents/skills/` and symlinks to harness-specific directories automatically. One install, every harness picks it up.

### AGENTS.md is the source of truth

`AGENTS.md` is the open-standard context file read by Pi, Codex, Cursor, Aider, and others. If you use Claude Code, `CLAUDE.md` is derived from AGENTS.md with a source header. If you use Cursor, `.cursor/rules/` is derived the same way.

### Process state machines are domain-specific

Engineering gets TDD + triage + PRD. Finance gets research → analyze → validate → report. Each domain has its own state machine with transition guards — not a generic plan-do-review.

### Custom skills fill the gaps

When no existing skill covers a need, the bootstrap skill generates a custom one. It interviews you per skill, follows the `writing-great-skills` principles (leading words, progressive disclosure, pruning), and writes a spec-compliant `SKILL.md` with a `generated-by: fat-skills-scaffold` metadata tag.

## Domains

Seven domains with curated catalogs today. Any domain works — the system falls back to live marketplace search + web search + custom skill generation for uncatalogued domains.

### Engineering

Software engineering & development workflows — building, testing, debugging, and shipping software.

**Marketplace skills** (8 recommended + 5 optional):

| Skill | Source | Description |
|---|---|---|
| tdd | mattpocock/skills | Test-driven development red-green-refactor |
| grill-with-docs | mattpocock/skills | Stress-test plans against domain model, build shared language |
| diagnosing-bugs | mattpocock/skills | Disciplined bug diagnosis loop |
| to-prd | mattpocock/skills | Turn context into a PRD |
| implement | mattpocock/skills | Implement a PRD or plan |
| improve-codebase-architecture | mattpocock/skills | Find deepening and refactoring opportunities |
| triage | mattpocock/skills | Issue triage state machine |
| to-issues | mattpocock/skills | Break plans into vertical-slice issues |
| codebase-design | mattpocock/skills | Design module interfaces and architecture |
| grill-me | mattpocock/skills | Non-code grilling session |
| handoff | mattpocock/skills | Context handoff between sessions |
| writing-great-skills | mattpocock/skills | Reference for writing skills well |
| typescript-advanced-types | wshobson/agents | Advanced TypeScript type patterns (conditional) |

**Custom skills**: none — well-covered domain.

**Process state machine**: TRIAGE → PLAN → BRANCH → TDD-LOOP → COMMIT → REVIEW → FIX → ASK-MERGE → DONE. Full PRD lifecycle: DISCOVERY → PRD → ISSUES → state machine. Bug path includes DIAGNOSE with feedback loop requirement.

**Extensions**: Pi-Astrolabe (AST tools, required), Pi-Simple-Subagents (subagent delegation, required), pi-mcp-adapter (recommended), Playwright MCP (for Claude Code/Cursor).

---

### Finance

Financial analysis & investment workflows — modeling, research, risk assessment, and reporting.

**Marketplace skills** (2 recommended + 2 optional):

| Skill | Source | Description |
|---|---|---|
| wind-find-finance | wind-skills | Financial data lookup and market research |
| yahoo-finance | stock-checker | Yahoo Finance data for US/international markets |
| finance-expert | pcl | Financial analysis and advisory |
| tushare-finance | tushare-skill | Chinese A-share market data (conditional: China market) |

**Custom skills** (generated for gaps):

| Skill | Description |
|---|---|
| financial-modeling | Build and validate DCF, LBO, merger, comparable, budget models |
| risk-assessment | Portfolio risk analysis, stress testing, scenario modeling, VaR/Sharpe |
| investment-memo | Structured investment memo and thesis writing |

**Process state machine**: QUESTION → RESEARCH → ANALYZE → VALIDATE → REPORT → REVIEW → PUBLISH. Every number traces to a source. Every projection is marked. Sensitivity analysis required before validation passes.

**Extensions**: pi-mcp-adapter (recommended), Alpha Vantage MCP (for Claude Code/Cursor).

---

### Content Creation & Personal Branding

Content creation & personal branding workflows — writing, social media strategy, audience growth, and SEO.

**Marketplace skills** (2 recommended + 2 optional):

| Skill | Source | Description |
|---|---|---|
| branding | kostja94/marketing-skills | Branding strategy development and brand identity |
| linkedin-personal-branding | schwepps/skills | LinkedIn personal branding and profile optimization |
| rebranding-strategy | kostja94/marketing-skills | Rebranding strategy and execution |
| social-media-image-sizes | branding5 | Reference for correct image dimensions across platforms |

**Custom skills** (generated for gaps):

| Skill | Description |
|---|---|
| content-calendar | Plan and schedule content across platforms |
| content-creation | Draft blog posts, social media, newsletters with brand voice |
| audience-research | Understand and grow your target audience |
| seo-optimization | SEO research and content optimization |

**Process state machine**: STRATEGY → PLAN → CREATE → REVIEW → PUBLISH → MEASURE → ITERATE. Brand voice check required before publish. Every piece has a call to action.

**Extensions**: pi-mcp-adapter (recommended).

---

### Go-to-Market & Business Strategy

Go-to-market & business strategy workflows — market research, positioning, launch planning, and metrics.

**Marketplace skills** (2 recommended):

| Skill | Source | Description |
|---|---|---|
| go-to-market-strategy | manojbajaj95/claude-gtm-plugin | GTM strategy development |
| go-to-market-planner | nicepkg/ai-workflow | Structured GTM planning framework |

**Custom skills** (generated for gaps — marketplace coverage is thin):

| Skill | Description |
|---|---|
| market-research | Competitive analysis, TAM/SAM/SOM, customer interviews |
| positioning | ICP definition, value prop, messaging framework |
| launch-planning | Launch plans with timelines, channels, owners, checklists |
| metrics-and-iteration | KPI tracking, attribution, iteration cycles |

**Process state machine**: RESEARCH → POSITION → PLAN → LAUNCH → MEASURE → ITERATE. Requires 5+ customer interviews before positioning. Positioning must be approved before planning.

**Extensions**: pi-mcp-adapter (recommended).

---

### Career Development

Career development workflows — planning, skill building, job search, and professional growth.

**Marketplace skills** (1 recommended + 2 optional):

| Skill | Source | Description |
|---|---|---|
| career-transitions | refoundai/lenny-skills | Career transition guidance and planning |
| career-changer-translator | paramchoudhary/resumeskills | Translate experience across careers |
| career-ops-job-search | aradotso/trending-skills | Job search operations and tracking |

**Custom skills** (generated for gaps):

| Skill | Description |
|---|---|
| career-assessment | Self-assessment of skills, values, interests, and trajectory |
| skill-gap-analysis | Identify gaps between current and target role; build learning plan |
| resume-and-profile | Build resumes, LinkedIn profiles, professional bios (ATS-friendly) |
| interview-prep | Prepare for behavioral, technical, and case interviews |

**Process state machine**: ASSESS → PLAN → ACT → REFLECT → ADJUST. Evidence-based — every resume claim backed by a specific example. Privacy-first — no sharing without consent.

**Extensions**: pi-mcp-adapter (recommended).

---

### Research & Knowledge Management

Research & knowledge management workflows — literature reviews, synthesis, note-taking, and academic workflows.

**Marketplace skills** (2 optional):

| Skill | Source | Description |
|---|---|---|
| obsidian-vault | mattpocock/skills | Obsidian vault management and knowledge graphs |
| teach | mattpocock/skills | Structured learning and teaching workflow |

**Custom skills** (generated for gaps):

| Skill | Description |
|---|---|
| literature-review | Structured literature reviews — search, screen, extract, synthesize |
| note-synthesis | Transform raw notes into structured, linked knowledge |
| research-question | Frame and refine research questions; identify methodology |
| source-validation | Evaluate credibility, detect bias, assess evidence quality |

**Process state machine**: QUESTION → SEARCH → EXTRACT → SYNTHESIZE → VALIDATE → PUBLISH. Every claim traces to a source. Bias checks at validation stage. Source diversity required.

**Extensions**: pi-mcp-adapter (recommended).

---

### Product Management

Product management workflows — PRDs, user stories, roadmap planning, feature prioritization.

**Marketplace skills** (4 recommended + 3 optional):

| Skill | Source | Description |
|---|---|---|
| to-prd | mattpocock/skills | Turn context into a PRD |
| grill-me | mattpocock/skills | Stress-test product ideas before committing |
| to-issues | mattpocock/skills | Break plans into vertical-slice issues |
| triage | mattpocock/skills | Issue triage state machine |
| grill-with-docs | mattpocock/skills | Grilling with codebase context |
| handoff | mattpocock/skills | Context handoff between sessions |
| persona-project-manager | googleworkspace/cli | Project management with persona-based planning |

**Custom skills** (generated for gaps):

| Skill | Description |
|---|---|
| roadmap-planning | Roadmaps with themes, epics, timelines; RICE/MoSCoW prioritization |
| user-research | Plan and synthesize user research; build personas |
| feature-spec | Detailed feature specs with user stories and acceptance criteria |

**Process state machine**: DISCOVERY → PRD → SPEC → PRIORITIZE → DELIVER → MEASURE → ITERATE. User-centric — every decision starts from the user's problem. Scope ruthlessly — ship smallest valuable slice first.

**Extensions**: Pi-Astrolabe (required), Pi-Simple-Subagents (required), pi-mcp-adapter (recommended).

## Cross-domain composition

Run `/bootstrap` once per domain. Each run adds to the project without overwriting:

| What | Re-run behavior |
|---|---|
| Skills | `npx skills add` is idempotent — skips already-installed |
| AGENTS.md | Append-only — new sections added, existing content preserved |
| CLAUDE.md | Regenerated from updated AGENTS.md (it's derived, not source-of-truth) |
| State machines | New domain adds its own section |
| Extensions | `pi install` skips already-installed |

Example: run `/bootstrap` for engineering, then run it again for finance. You get both sets of skills, both state machines in AGENTS.md, and finance-specific extensions added.

## Custom domains

Select "Other" in the interview and describe your goal in your own words. The system:

1. Searches skills.sh across multiple keyword angles
2. Runs web searches for relevant skills, MCP servers, and tools
3. Generates custom skills for every gap it identifies

No catalog needed — the system works for any domain.

## Harness compatibility

| Harness | Skills location | Context file | Extensions |
|---|---|---|---|
| **Pi** | `.pi/skills/`, `.agents/skills/` | `AGENTS.md` | Pi packages, pi-mcp-adapter |
| **Claude Code** | `.claude/skills/`, `.agents/skills/` | `CLAUDE.md` (derived) | MCP servers (`.mcp.json`) |
| **Cursor** | `.cursor/rules/`, `.agents/skills/` | `.cursor/rules/` (derived) | MCP servers |
| **Codex** | `.agents/skills/` | `AGENTS.md` | Built-in tools |
| **Gemini CLI** | `.agents/skills/` | `AGENTS.md` | MCP servers |

`npx skills add` handles cross-harness installation — it writes to `.agents/skills/` and symlinks to harness-specific directories automatically.

CLAUDE.md and `.cursor/rules/` are derived from AGENTS.md with a source header:
```html
<!-- Derived from AGENTS.md by fat-skills-scaffold. Edit AGENTS.md and re-run /bootstrap to update. -->
```

## Extending the system

### Add a new domain

1. Create `catalog/<domain>.yaml` following the existing catalog format
2. Create `state-machines/<domain>.md` with the domain's process state machine and transition guards
3. Create `templates/<domain>.md` as the AGENTS.md template with `{{slot}}` placeholders
4. Add extension recommendations to `extensions/pi.yaml` and the catalog's `extensions` section
5. Submit a PR

### Catalog format

```yaml
domain: my-domain
description: What this domain covers

skills:
  mattpocock:                    # or any source name
    - name: skill-name
      skill: owner/repo@skill    # skills.sh identifier
      description: When to use it
      tags: [tag1, tag2]
      condition: "optional condition"  # e.g. "tech_stack includes x"
      recommended: true/false

  marketplace:
    - name: another-skill
      skill: owner/repo@skill
      # ...same fields

  custom:                        # skills to generate for gaps
    - name: gap-skill
      description: What it should do
      tags: [tag1, tag2]
      interview_questions:
        - Question to ask the user

extensions:
  pi:
    required:
      - name: Extension-Name
        source: git:github.com/org/repo
        description: What it adds
    recommended:
      - name: Optional-Extension
        source: npm:package-name
        description: What it adds

  claude-code:
    mcp-servers:
      - name: server-name
        config: { command: "npx", args: ["-y", "package"] }
        description: What it provides

  cursor:
    mcp-servers:
      - name: server-name
        config: { command: "npx", args: ["-y", "package"] }
```

### Template format

Templates use `{{slot_name}}` placeholders that the bootstrap skill fills from interview answers:

- `{{communication_style}}` — caveman / normal / verbose
- `{{domain_context}}` — freeform context from the chat phase
- `{{state_machine}}` — injected from `state-machines/<domain>.md`
- `{{tech_stack}}` — from interview
- `{{architecture}}` — from interview
- `{{issue_tracker_config}}` — from interview
- `{{triage_labels_config}}` — defaults or from interview
- `{{domain_docs_config}}` — defaults or from interview

### State machine format

Markdown with an ASCII state flow diagram, transition guards table, and detailed subsections for each phase. See `state-machines/engineering.md` for the most complete example.

## Project structure

```
fat-skills-scaffold/
├── package.json                          # Pi package manifest
├── skills/
│   └── bootstrap/                        # The meta-skill
│       ├── SKILL.md                      # 4-phase workflow
│       └── references/
│           ├── domain-catalog.md         # How to read catalog YAML
│           ├── skill-sources.md          # skills.sh search patterns
│           ├── cross-harness.md          # Harness compat & extension mapping
│           └── custom-skills.md          # Writing-great-skills principles
├── catalog/                              # 7 domain catalogs
│   ├── engineering.yaml
│   ├── finance.yaml
│   ├── branding.yaml
│   ├── gtm.yaml
│   ├── career.yaml
│   ├── research.yaml
│   └── product.yaml
├── state-machines/                       # 7 domain state machines
│   ├── engineering.md
│   ├── finance.md
│   ├── branding.md
│   ├── gtm.md
│   ├── career.md
│   ├── research.md
│   └── product.md
├── templates/                            # 7 AGENTS.md templates
│   ├── engineering.md
│   ├── finance.md
│   ├── branding.md
│   ├── gtm.md
│   ├── career.md
│   ├── research.md
│   └── product.md
└── extensions/
    └── pi.yaml                           # Pi extension recommendations per domain
```

## License

MIT
