# Fat Skills Scaffold — Architecture Plan v2

## 1. Concept

A **harness-agnostic meta-skill** that bootstraps a complete agent setup for any goal. The user states what they want to achieve, answers a structured interview, and the system generates a bespoke agent environment with curated skills, tailored context files, and tool/extension recommendations.

**Key principles:**
- **Harness-agnostic by default** — skills go to `.agents/skills/` (universal), context to `AGENTS.md` (open standard), harness-specific context files optionally derived
- **Pi-native as first-class** — when the user runs Pi, we also generate Pi-specific setup (extensions, `.pi/skills/` symlinks, `.pi/settings.json`)
- **Re-runnable for cross-domain** — each run targets one domain; run again to compose
- **Skill creation delegates to experts** — use `writing-great-skills` (Matt Pocock) for custom skill generation rather than reinventing

---

## 2. Harness Compatibility

### The landscape

| Harness | Skills Location | Context File | Extensions/Tools |
|---|---|---|---|
| **Pi** | `.pi/skills/`, `~/.pi/agent/skills/` | `AGENTS.md` | Pi packages (git/npm), extensions (`.ts`), `pi-mcp-adapter` |
| **Claude Code** | `.claude/skills/` + `.agents/skills/` | `CLAUDE.md` | MCP servers (`.mcp.json`, `~/.claude.json`) |
| **Cursor** | `.cursor/rules/` | `.cursorrules` (legacy) | MCP servers, Cursor extensions |
| **Codex** | `.agents/skills/` | `AGENTS.md` | Built-in tools |
| **Gemini CLI** | `.agents/skills/` | `AGENTS.md` | MCP servers |
| **AiderDesk** | `.aiderdesk/skills/` → `.agents/skills/` | varies | varies |

### The universal path

`npx skills add` already handles cross-harness installation:
- Installs to `.agents/skills/` (universal, spec-compliant)
- Symlinks to harness-specific dirs (`.claude/skills/`, `.pi/skills/`, etc.)
- Pi reads from `.agents/skills/` natively (via discovery rules)

**Our approach**: Use `npx skills add` as the skill installation mechanism. It's already cross-harness. Our scaffold adds the **curation, context, and configuration** layer on top.

### Extension mapping

Different harnesses have different extension/tool ecosystems, but there are functional equivalents:

| Capability | Pi | Claude Code | Cursor | Generic MCP |
|---|---|---|---|---|
| Code intelligence (AST) | Pi-Astrolabe | — | — | `typescript-mcp-server` |
| Subagent delegation | Pi-Simple-Subagents | — | — | — |
| Web search | pi-web-access (pkg) | — | — | brave-search-mcp |
| Browser automation | — | browser-mcp | playwright-mcp | playwright-mcp |
| Database | — | postgres-mcp | — | postgres-mcp |
| Generic tool bridge | pi-mcp-adapter | native MCP | native MCP | — |

**Our approach**: The catalog maps each capability to harness-specific implementations. The bootstrap skill generates the right config for the user's harness(es).

---

## 3. Package Structure

```
fat-skills-scaffold/
├── package.json                     # pi-package manifest
├── skills/
│   └── bootstrap/                   # The meta-skill — main entry point
│       ├── SKILL.md                 # Interview → plan → write workflow
│       └── references/
│           ├── domain-catalog.md    # How to use the catalog
│           ├── skill-sources.md     # How to search skills.sh, known repos
│           ├── cross-harness.md     # Harness compatibility & extension mapping
│           └── custom-skills.md     # How to use writing-great-skills for creation
├── catalog/                         # Domain → skill/capability mappings
│   ├── engineering.yaml
│   ├── finance.yaml
│   ├── branding.yaml
│   ├── gtm.yaml
│   ├── career.yaml
│   ├── research.yaml
│   └── product.yaml
├── state-machines/                  # Domain process state machines
│   ├── engineering.md
│   ├── finance.md
│   ├── branding.md
│   ├── gtm.md
│   ├── career.md
│   ├── research.md
│   └── product.md
├── templates/                       # AGENTS.md templates per domain
│   ├── engineering.md
│   ├── finance.md
│   ├── branding.md
│   ├── gtm.md
│   ├── career.md
│   ├── research.md
│   └── product.md
└── extensions/                      # Harness-specific extension configs
    └── pi.yaml                      # Pi extension recommendations per domain
```

---

## 4. The Bootstrap Skill — Core Workflow

### Phase 1: Goal Discovery (Hybrid Interview)

**Structured interview** (`pi-interview`):

1. **What's your primary goal?** — select from catalog domains + "Other (describe)"
2. **Which harnesses do you use?** — multi-select: Pi, Claude Code, Cursor, Codex, Gemini CLI, Other
3. **Team size?** — solo / small team / large team
4. **Project directory?** — where to write output
5. **Communication style?** — caveman / normal / verbose
6. **Issue tracking?** — GitHub / GitLab / kanban-md / Linear / local / other

**Freeform chat** — follow-up questions to understand nuance, constraints, specific sub-domains.

### Phase 2: Research & Planning

1. **Catalog lookup** — match domain to `catalog/*.yaml` recommendations. If no catalog exists for the domain, skip to step 2.
2. **Marketplace search** — `npx skills search <domain keywords>` for complementary skills. For custom domains, search across multiple keyword angles. If skills.sh is down, inform the user and suggest re-running later.
3. **Web search** (especially for custom domains) — search for relevant skills, MCP servers, tools, and workflows for the domain.
4. **Extension mapping** — based on selected harnesses, map capabilities to extensions/MCP servers
5. **Gap analysis** — identify needs not covered by existing skills
6. **Present plan** via interview form — show (install counts fetched live from `npx skills search`):

For each recommended skill:
| Skill | Source | Installs | Link | Included? |
|---|---|---|---|---|
| tdd | mattpocock/skills | 220K | [skills.sh/...](...) | ✅ (recommended) / ☐ (override) |
| diagnosing-bugs | mattpocock/skills | 180K | [skills.sh/...](...) | ✅ / ☐ |
| wind-find-finance | wind-skills | 6.8K | [skills.sh/...](...) | ☐ (optional) |

User can:
- **Override** any recommendation (uncheck included, check optional)
- **Click links** to review skills on skills.sh
- **Add custom skills** they know about (text input for `owner/repo@skill`)
- **Mark gaps** for custom skill generation

Also show:
- Extensions/MCP servers recommended for their harness
- AGENTS.md template preview
- Process state machine preview
- Directory structure that will be created

6. **Get approval** — user reviews and confirms before anything is written

### Phase 3: Generation & Installation

1. **Install skills** — for each approved skill:
   ```bash
   npx skills add <owner/repo@skill> --yes
   ```
   This handles cross-harness installation automatically.

2. **Install Pi extensions** (if Pi is selected as harness):
   ```bash
   pi install git:github.com/tech4ge/Pi-Astrolabe
   pi install git:github.com/tech4ge/Pi-Simple-Subagents
   ```

3. **Configure MCP servers** (if Claude Code, Cursor, or other MCP-using harness):
   Write appropriate `.mcp.json` entries for recommended MCP servers.

4. **Generate custom skills** (for gaps):
   - Use `writing-great-skills` principles (from Matt Pocock's reference)
   - Interview the user per custom skill via `pi-interview`
   - Write to `.agents/skills/<skill-name>/SKILL.md`
   - `npx skills add` will symlink to harness-specific dirs on next run

5. **Generate AGENTS.md** — merge domain template with user context

6. **Optionally derive harness-specific context**:
   - `CLAUDE.md` if Claude Code selected — full copy of AGENTS.md with header:
     `<!-- Derived from AGENTS.md by fat-skills-scaffold. Edit AGENTS.md and re-run /bootstrap to update. -->`
   - `.cursor/rules/` if Cursor selected — derived from AGENTS.md

7. **Create directory structure** — `.scratch/`, `docs/agents/`, domain-specific dirs

8. **Write supporting docs** — `docs/agents/issue-tracker.md`, `docs/agents/triage-labels.md`, `docs/agents/domain.md`

### Phase 4: Verification & Handoff

1. **Validate** — check skills load, AGENTS.md parses
2. **Summarize** — what was created, how to use it
3. **Next steps** — suggest initial prompts, skill commands to try

---

## 5. Skill Catalog Format

Each `catalog/*.yaml` maps a domain to skills and extensions:

```yaml
# catalog/engineering.yaml
domain: engineering
description: Software engineering & development workflows

skills:
  # Grouped by source, ordered by install count (descending)
  mattpocock:
    - name: tdd
      skill: mattpocock/skills@tdd
      description: Test-driven development red-green-refactor
      tags: [testing, development, workflow]
      recommended: true
      # Install counts fetched live via `npx skills search` during Phase 2
      # Catalog stores identifiers + recommendations; counts are never static

    - name: grill-with-docs
      skill: mattpocock/skills@grill-with-docs
      description: Stress-test plans against domain model, build shared language
      tags: [planning, review, context]
      recommended: true

    - name: diagnosing-bugs
      skill: mattpocock/skills@diagnosing-bugs
      description: Disciplined bug diagnosis loop
      tags: [debugging, workflow]
      recommended: true

    - name: to-prd
      skill: mattpocock/skills@to-prd
      description: Turn context into a PRD
      tags: [planning, product]
      recommended: true

    - name: improve-codebase-architecture
      skill: mattpocock/skills@improve-codebase-architecture
      description: Find deepening and refactoring opportunities
      tags: [architecture, refactoring]
      recommended: true

    - name: implement
      skill: mattpocock/skills@implement
      description: Implement a PRD or plan
      tags: [implementation, workflow]
      recommended: true

    - name: triage
      skill: mattpocock/skills@triage
      description: Issue triage state machine
      tags: [issues, workflow]
      recommended: true

    - name: to-issues
      skill: mattpocock/skills@to-issues
      description: Break plans into vertical-slice issues
      tags: [planning, issues]
      recommended: true

    - name: codebase-design
      skill: mattpocock/skills@codebase-design
      description: Design module interfaces and architecture
      tags: [architecture, design]
      recommended: false  # optional, for larger projects

    - name: grill-me
      skill: mattpocock/skills@grill-me
      description: Non-code grilling session
      tags: [planning, review]
      recommended: false

    - name: handoff
      skill: mattpocock/skills@handoff
      description: Context handoff between sessions
      tags: [workflow, continuity]
      recommended: false

    - name: writing-great-skills
      skill: mattpocock/skills@writing-great-skills
      description: Reference for writing skills well
      tags: [meta, skills]
      recommended: false  # meta-skill, useful for skill authors

  # Additional marketplace skills
  marketplace:
    - name: typescript-advanced-types
      skill: wshobson/agents@typescript-advanced-types
      description: Advanced TypeScript type patterns
      tags: [typescript, types]
      condition: "tech_stack includes typescript"
      recommended: false

  # Custom skills to generate for gaps
  custom: []
    # No gaps for engineering — well-covered domain

# Harness-specific extensions
extensions:
  pi:
    required:
      - name: Pi-Astrolabe
        source: git:github.com/tech4ge/Pi-Astrolabe
        description: Tree-sitter code intelligence (AST tools)

      - name: Pi-Simple-Subagents
        source: git:github.com/tech4ge/Pi-Simple-Subagents
        description: Subagent delegation via RPC

    recommended:
      - name: pi-mcp-adapter
        source: npm:pi-mcp-adapter
        description: MCP server integration without context bloat

  claude-code:
    mcp-servers:
      - name: playwright
        config: { command: "npx", args: ["-y", "@anthropic-ai/mcp-playwright"] }
        description: Browser automation for testing

  cursor:
    mcp-servers:
      - name: playwright
        config: { command: "npx", args: ["-y", "@anthropic-ai/mcp-playwright"] }
```

```yaml
# catalog/finance.yaml
domain: finance
description: Financial analysis & investment workflows

skills:
  marketplace:
    - name: wind-find-finance
      skill: wind-information-co-ltd/wind-skills@wind-find-finance-skill
      description: Financial data lookup and market research
      tags: [data, research]
      recommended: true

    - name: finance-expert
      skill: personamanagmentlayer/pcl@finance-expert
      description: Financial analysis and advisory
      tags: [analysis, advisory]
      recommended: false

    - name: tushare-finance
      skill: stanleychanh/tushare-finance-skill-for-claude-code@tushare-finance
      description: Chinese A-share market data via Tushare
      tags: [data, china, stocks]
      condition: "market includes china"
      recommended: false

    - name: yahoo-finance
      skill: gracefullight/stock-checker@yahoo-finance
      description: Yahoo Finance data lookup
      tags: [data, us, stocks]
      recommended: true

  custom:
    - name: financial-modeling
      description: Build and validate financial models (DCF, LBO, comps)
      tags: [modeling, analysis]
      interview_questions:
        - What types of models do you build? (DCF, LBO, merger, budget)
        - What data sources do you use?
        - What's your output format? (Excel, report, dashboard)

    - name: risk-assessment
      description: Portfolio risk analysis, stress testing, scenario modeling
      tags: [risk, portfolio]
      interview_questions:
        - What risk metrics do you track? (VaR, Sharpe, max drawdown)
        - What time horizons?
        - Any regulatory requirements?

    - name: investment-memo
      description: Structured investment memo / thesis writing
      tags: [writing, analysis, communication]
      interview_questions:
        - What's your memo format? (long-form, 1-pager, pitch deck)
        - What sections are required?
        - Who's the audience?

extensions:
  pi:
    recommended:
      - name: pi-mcp-adapter
        source: npm:pi-mcp-adapter
        description: Connect to financial data MCP servers

  claude-code:
    mcp-servers:
      - name: alpha-vantage
        config: { command: "npx", args: ["-y", "alpha-vantage-mcp"] }
        description: Alpha Vantage financial data API
```

---

## 6. Cross-Domain Composition via Re-Running

The bootstrap skill is **re-runnable**. Each invocation targets one domain:

```
# First run — set up engineering
/bootstrap
> "I'm building a SaaS product — need engineering + go-to-market"
> Selects: engineering (primary), gtm (secondary)

# Output: engineering skills + AGENTS.md with engineering state machine
# User works for a while...

# Second run — add finance analysis
/bootstrap
> "I also need financial modeling for investor reporting"
> Selects: finance

# Output: ADDS finance skills, APPENDS to AGENTS.md (never overwrites)
# Process state machine gets a second section for financial analysis
```

**Idempotency rules:**
- Skills: `npx skills add` is idempotent (skips if already installed)
- AGENTS.md: **never overwritten** — new sections appended, existing sections preserved
- Extensions: `pi install` is idempotent
- Directories: `mkdir -p` is idempotent
- State machines: new domains add new sections, never replace existing

---

## 7. Custom Skill Generation

Rather than building our own skill-creation interview, we **delegate to the expert**:

1. **Detect gaps** — needs not covered by catalog + marketplace skills
2. **Present gaps to user** — "These capabilities aren't covered by existing skills. Want to create custom skills for them?"
3. **For each custom skill**, use `writing-great-skills` principles:
   - Leading words, progressive disclosure, information hierarchy
   - Completion criteria, no-ops elimination, pruning
4. **Interview per skill** (`pi-interview`):
   - What should this skill do?
   - When should it trigger? (→ description + leading words)
   - What are the key steps? (→ in-skill steps)
   - What reference material is needed? (→ external references)
   - What completion criteria for each step?
5. **Write SKILL.md** — following Agent Skills spec, with:
   - `metadata: { generated-by: "fat-skills-scaffold" }` in frontmatter
   - A notice at the top of the body: _"This skill was generated by fat-skills-scaffold. Test thoroughly before relying on it in production workflows."_
6. **Install** — write to `.agents/skills/` so all harnesses pick it up

The `writing-great-skills` reference is bundled in the scaffold's `references/custom-skills.md` — it distills Matt Pocock's principles into a practical guide the agent follows when generating.

---

## 8. Skill Discovery UX

The plan presentation uses `pi-interview` with a table-like layout:

```
┌─────────────────────────────────────────────────────────────┐
│  Recommended Skills for: Financial Analysis & Investment    │
├──────────────────┬──────────┬────────────────┬───────────────┤
│ Skill            │ Installs │ Source         │ Include?      │
├──────────────────┼──────────┼────────────────┼───────────────┤
│ wind-finance     │ 6.8K     │ wind-skills    │ ✅ Recommended│
│ yahoo-finance    │ 4.0K     │ stock-checker  │ ✅ Recommended│
│ finance-expert   │ 5.4K     │ pcl            │ ☐ Optional    │
│ tushare-finance  │ 4.7K     │ tushare-skill  │ ☐ (CN only)  │
├──────────────────┴──────────┴────────────────┴───────────────┤
│ Click any skill name to review on skills.sh                 │
│ Uncheck to override, check optional to add                  │
├─────────────────────────────────────────────────────────────┤
│ Custom Skills to Generate (gaps):                           │
│  ✅ financial-modeling — Build & validate models             │
│  ✅ risk-assessment — Portfolio risk analysis                │
│  ☐ investment-memo — Structured memo writing                │
├─────────────────────────────────────────────────────────────┤
│ Extensions for Pi:                                          │
│  ✅ pi-mcp-adapter (MCP server bridge)                     │
│  ☐ Pi-Astrolabe (AST tools — overkill for finance?)        │
├─────────────────────────────────────────────────────────────┤
│ Extensions for Claude Code:                                 │
│  ✅ alpha-vantage-mcp (financial data)                     │
│  ☐ playwright-mcp (browser automation)                     │
└─────────────────────────────────────────────────────────────┘
```

Each skill name is a link (in the interview's media/HTML) to its skills.sh page for review.

---

## 9. AGENTS.md Template System

Templates are domain-specific markdown files with **slot syntax** (`{{slot_name}}`):

```markdown
# templates/finance.md
## Communication

{{communication_style}} mode.

## Domain Context

{{domain_context}}

## Process State Machine

{{state_machine}}

## Tool Rules

1. **Always cite data sources.** Every number in a report must trace to a source.
2. **Distinguish fact from projection.** Mark assumptions and projections clearly.
3. **Recency matters.** Check data timestamps before relying on them.
4. **No redundant `cd`.** Working directory is already set.

## Tech Stack

{{tech_stack}}

## Architecture

{{architecture}}

## Agent Skills

### Issue Tracker

{{issue_tracker_config}}

### Triage Labels

{{triage_labels_config}}

### Domain Docs

{{domain_docs_config}}

## Version Control Discipline

### Check main state before work
If there are uncommitted files, ask the user what they want to do.

### Always branch from main for new analyses
Branch for each new analysis or report.

### ALWAYS ask before publishing
Never publish a report or share with stakeholders without asking first.
```

---

## 10. State Machine Templates

Each domain gets a tailored state machine. Example for **go-to-market**:


## Process State Machine

Every GTM initiative follows this sequence:

```
RESEARCH ──► POSITION ──► PLAN ──► LAUNCH ──► MEASURE ──► ITERATE
  │              │           │         │          │           │
  │              │           │         │          │           └─ feed data back into RESEARCH
  │              │           │         │          └─ track KPIs, attribute results
  │              │           │         └─ execute launch plan, coordinate teams
  │              │           └─ detailed launch plan, timelines, owners
  │              └─ define positioning, messaging, ICP
  └─ market research, competitive analysis, customer interviews
```

### Transition Guards

| Transition | Guard |
|---|---|
| RESEARCH → POSITION | At least 5 customer interviews completed, competitive landscape mapped |
| POSITION → PLAN | Positioning statement approved by stakeholder |
| PLAN → LAUNCH | Launch plan has clear KPIs, timeline, and assigned owners |
| LAUNCH → MEASURE | Launch materials deployed, tracking in place |
| MEASURE → ITERATE | Minimum 1 week of post-launch data collected |


---

## 11. Implementation Plan

### Phase 1: Package Scaffold & Bootstrap Skill (Core)

1. Create `package.json` with pi manifest
2. Write `bootstrap/SKILL.md` — the 4-phase workflow
3. Write `bootstrap/references/domain-catalog.md` — how to read & use catalog
4. Write `bootstrap/references/skill-sources.md` — skills.sh, known repos, search patterns
5. Write `bootstrap/references/cross-harness.md` — harness compat, extension mapping
6. Write `bootstrap/references/custom-skills.md` — distilled writing-great-skills principles

### Phase 2: Engineering Domain (Prove It Works)

7. Write `catalog/engineering.yaml` — Matt Pocock skills as primary, tech4ge Agent-Scaffold as alt
8. Write `state-machines/engineering.md` — TDD + triage + PRD lifecycle
9. Write `templates/engineering.md` — AGENTS.md template
10. Write `extensions/pi.yaml` — Pi extension recommendations
11. Test end-to-end: `/bootstrap` → engineering domain → project setup

### Phase 3: Non-Engineering Domains

12. Write `catalog/finance.yaml` + `state-machines/finance.md` + `templates/finance.md`
13. Write `catalog/gtm.yaml` + `state-machines/gtm.md` + `templates/gtm.md`
14. Write `catalog/branding.yaml` + `state-machines/branding.md` + `templates/branding.md`
15. Write `catalog/career.yaml` + `state-machines/career.md` + `templates/career.md`
16. Write `catalog/research.yaml` + `state-machines/research.md` + `templates/research.md`
17. Write `catalog/product.yaml` + `state-machines/product.md` + `templates/product.md`

### Phase 4: Cross-Harness & Polish

18. Test cross-harness installation (skills.sh → .agents/skills/ → symlinks)
19. Test CLAUDE.md derivation from AGENTS.md
20. Test .cursor/rules/ derivation
21. Test MCP server config generation for non-Pi harnesses
22. Test re-running for cross-domain composition

### Phase 5: Distribution

23. Publish to GitHub as `tech4ge/fat-skills-scaffold`
24. Add to pi settings: `pi install git:github.com/tech4ge/fat-skills-scaffold`
25. Later: publish to npm

---

## 12. Key Design Decisions (v2)

| Decision | Choice | Rationale |
|---|---|---|
| **Harness scope** | Universal (`.agents/skills/`) + Pi-specific extras | Skills.sh already handles cross-harness; we add curation + context |
| **Skill installation** | `npx skills add` (delegates to skills.sh) | Already cross-harness, handles symlinks, idempotent |
| **Context file** | AGENTS.md primary, derive CLAUDE.md/.cursor/rules | AGENTS.md is the open standard; harness files are derived subsets |
| **Custom skill creation** | Delegate to `writing-great-skills` principles | Matt Pocock's 220K-installed reference > reinventing |
| **Skill discovery UX** | Ranked by installs, links to review, override toggles | User makes informed decisions with easy overrides |
| **Cross-domain** | Re-run bootstrap per domain, append to AGENTS.md | Composable, idempotent, no complex multi-domain interview |
| **Extensions** | Per-harness mapping in catalog YAML | Different harnesses have different extension ecosystems |
| **Process state machines** | Fully tailored per domain, composable via re-running | Each domain has distinct workflow; re-run appends new sections |
| **Updates** | Skills refreshable (npx skills add is idempotent), AGENTS.md never overwritten | Safe upgrades, no lost customizations |
| **Custom domain handling** | Catalog as baseline, live marketplace + web search for any domain | System works for any goal, not just catalogued domains |
| **Install counts** | Always fetched live via `npx skills search` | Accurate popularity data; catalog never stores stale counts |
| **CLAUDE.md derivation** | Full AGENTS.md copy + source header comment | Single source of truth (AGENTS.md); CLAUDE.md is derived |
| **Generated skill provenance** | `metadata.generated-by` tag + testing notice in body | Traceable origin, user warned to test thoroughly |

---

## 13. Resolved Design Decisions

1. **Marketplace search reliability** — If skills.sh is down, tell the user and let them re-run later. No caching, no fallback — keep it simple. The catalog YAML provides the baseline recommendations; live search is additive.

2. **Custom skill quality** — Add `metadata: { generated-by: "fat-skills-scaffold" }` to the frontmatter of generated skills. Include a prominent notice in the skill body: _"This skill was generated by fat-skills-scaffold. Test thoroughly before relying on it in production workflows."_ No automated validation beyond spec compliance (valid YAML frontmatter, required fields present).

3. **CLAUDE.md derivation** — Full copy of AGENTS.md into CLAUDE.md, with a header comment noting the source: `<!-- Derived from AGENTS.md by fat-skills-scaffold. Edit AGENTS.md and re-run /bootstrap to update. -->` On re-runs, the entire CLAUDE.md is regenerated from the current AGENTS.md (since CLAUDE.md is a derived artifact, not a source of truth).

4. **Custom domain handling** — When no catalog exists for the user's domain, fall back to: (a) full `npx skills search` across multiple keyword angles, (b) web search for relevant skills, MCP servers, and tools, (c) custom skill generation for all gaps. The catalog is a convenience, not a gate — the system works for any domain.

5. **Install count freshness** — Always do a live `npx skills search` to get current install counts. The catalog YAML stores skill identifiers and recommendations, but counts are fetched live during the planning phase. This ensures the user always sees accurate popularity data for informed decisions.
