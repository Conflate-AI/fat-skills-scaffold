---
name: bootstrap
description: Bootstrap a complete agent setup for any goal. Use when you want to configure a project with curated skills, tailored AGENTS.md, process state machines, and harness-specific extensions. Run once per domain — re-run to compose multiple domains.
---

# Bootstrap

Set up a complete, goal-driven agent environment for a project. The user states a goal, you interview them, research the best skills and tools, present a plan for approval, then write everything into their project directory.

Each run targets **one domain**. Re-run `/bootstrap` to compose additional domains — skills are idempotent, AGENTS.md is append-only (never overwritten), and state machines compose by adding new sections.

## Process

### Phase 1: Goal Discovery

Run a structured interview using the `interview` tool with these questions:

1. **Primary goal** — single select from the catalog domains plus "Other (describe)":
   - Software Engineering
   - Financial Analysis & Investment
   - Content Creation & Personal Branding
   - Go-to-Market & Business Strategy
   - Career Development
   - Research & Knowledge Management
   - Product Management
   - Other (describe in your own words)

2. **Harnesses** — multi-select: Pi, Claude Code, Cursor, Codex, Gemini CLI, Other

3. **Team size** — solo / small team (2-5) / large team (6+)

4. **Project directory** — absolute path where output should be written. Default: current working directory.

5. **Communication style** — caveman (ultra-compressed) / normal / verbose

6. **Issue tracking** — GitHub / GitLab / kanban-md / Linear / local markdown / other

After the structured interview, have a **freeform chat** to understand:
- Specific sub-domains or focus areas
- Tools and platforms already in use
- Constraints or preferences
- What "done" looks like for them

### Phase 2: Research & Planning

#### 2.1 Catalog lookup

Read the catalog file for the selected domain:

```
Read: catalog/<domain>.yaml (relative to this skill's directory)
```

The catalog maps the domain to recommended skills, grouped by source, with descriptions, tags, conditions, and whether each is recommended or optional. If no catalog exists for the domain (e.g. "Other"), skip to 2.2.

#### 2.2 Marketplace search

Run live marketplace searches to find complementary skills and get current install counts:

```bash
npx skills search "<domain keyword 1>"
npx skills search "<domain keyword 2>"
npx skills search "<domain keyword 3>"
```

Use 2-4 varied keyword angles per domain. Merge results with catalog recommendations, deduplicating by skill identifier.

If `npx skills search` fails (skills.sh down), inform the user and note that only catalog recommendations are available. Suggest re-running later for full marketplace coverage.

#### 2.3 Web search (especially for custom domains)

For "Other" domains or domains with sparse marketplace coverage, run web searches:

```
web_search with queries: [
  "best agent skills for <domain>",
  "MCP servers for <domain> workflows",
  "<domain> AI agent tools and extensions"
]
```

#### 2.4 Extension mapping

Based on the user's selected harnesses, map capabilities to extensions. Read:

```
Read: references/cross-harness.md (relative to this skill's directory)
```

For Pi: read `extensions/pi.yaml` for extension recommendations.
For Claude Code / Cursor: prepare MCP server config entries.

#### 2.5 Gap analysis

Identify capabilities the user needs that aren't covered by catalog + marketplace skills. These will be candidates for custom skill generation.

#### 2.6 Present plan

Use the `interview` tool to present the plan for approval. Show:

**Skills to install** — a table with columns:
- Skill name (with link to `https://skills.sh/<owner>/<repo>/<skill>`)
- Source (owner/repo)
- Installs (from live search)
- Included? (✅ recommended / ☐ optional) — user can toggle

**Custom skills to generate** — for each gap:
- Skill name
- Description
- Generate? (✅ / ☐) — user can toggle

**Extensions** — for each selected harness, show recommended extensions with toggle.

**Preview** — show the AGENTS.md template and process state machine that will be used.

**Add custom skills** — provide a text input for the user to add `owner/repo@skill` identifiers they already know about.

Wait for the user to approve or adjust the plan before proceeding.

### Phase 3: Generation & Installation

#### 3.1 Install marketplace/catalog skills

For each approved skill:

```bash
cd <project_directory> && npx skills add <owner/repo@skill> --yes
```

This installs to `.agents/skills/` (universal) and symlinks to harness-specific directories automatically. It is idempotent — already-installed skills are skipped.

#### 3.2 Install Pi extensions

If Pi is among the selected harnesses, install recommended extensions:

```bash
pi install git:github.com/tech4ge/Pi-Astrolabe
pi install git:github.com/tech4ge/Pi-Simple-Subagents
```

Only install extensions the user approved in the plan. Skip if already installed.

#### 3.3 Configure MCP servers

If Claude Code, Cursor, or other MCP-using harnesses are selected, write MCP server config entries to the appropriate file:

- **Claude Code**: append to `.mcp.json` in project root
- **Cursor**: append to `.cursor/mcp.json` or project MCP config
- **Shared**: write to `.mcp.json` (read by most MCP clients)

Format:

```json
{
  "mcpServers": {
    "<server-name>": {
      "command": "npx",
      "args": ["-y", "<package>"]
    }
  }
}
```

Only include MCP servers the user approved.

#### 3.4 Generate custom skills

For each custom skill the user approved:

1. Interview the user using `pi-interview`:
   - What should this skill do? (1-2 sentences)
   - When should it trigger? (description keywords and scenarios)
   - What are the key steps? (ordered list)
   - What completion criteria for each step?
   - What reference material is needed? (templates, glossaries, examples)
   - Should it be model-invoked or user-invoked? (`disable-model-invocation`)

2. Follow the `writing-great-skills` principles — read:

   ```
   Read: references/custom-skills.md (relative to this skill's directory)
   ```

   Apply: leading words, progressive disclosure, information hierarchy, pruning, no-op elimination.

3. Write the SKILL.md following the Agent Skills specification:

   ```yaml
   ---
   name: <skill-name>
   description: <description with trigger keywords>
   metadata:
     generated-by: "fat-skills-scaffold"
   ---
   ```

   Include this notice at the top of the body:

   > ⚠️ This skill was generated by fat-skills-scaffold. Test thoroughly before relying on it in production workflows.

4. Write to `<project_directory>/.agents/skills/<skill-name>/SKILL.md`

5. Move any reference material to sibling files in the skill directory (progressive disclosure).

#### 3.5 Generate AGENTS.md

Read the domain template:

```
Read: templates/<domain>.md (relative to this skill's directory)
```

Fill in the template slots using interview answers:

- `{{communication_style}}` — from Phase 1
- `{{domain_context}}` — from freeform chat
- `{{state_machine}}` — from `state-machines/<domain>.md`
- `{{tech_stack}}` — from interview
- `{{architecture}}` — from interview
- `{{issue_tracker_config}}` — from interview
- `{{triage_labels_config}}` — defaults or from interview
- `{{domain_docs_config}}` — defaults or from interview

**If AGENTS.md already exists** (re-run for cross-domain): do NOT overwrite. Instead, append new sections that don't conflict with existing content. Merge the new domain's state machine as an additional section. Preserve all existing customizations.

**If no AGENTS.md exists**: create it with the filled template.

#### 3.6 Derive harness-specific context

For each selected harness (beyond the universal AGENTS.md):

**Claude Code** — if selected:
- Write `CLAUDE.md` as a full copy of AGENTS.md
- Prepend: `<!-- Derived from AGENTS.md by fat-skills-scaffold. Edit AGENTS.md and re-run /bootstrap to update. -->`
- On re-runs, regenerate CLAUDE.md entirely (it's derived, not source-of-truth)

**Cursor** — if selected:
- Write `.cursor/rules/fat-skills-scaffold.md` with the AGENTS.md content
- Prepend the same derivation comment

#### 3.7 Create directory structure

```bash
mkdir -p <project_directory>/.scratch
mkdir -p <project_directory>/docs/agents
```

Add domain-specific directories if relevant (e.g. `models/` for finance, `content/` for branding).

#### 3.8 Write supporting docs

Write to `<project_directory>/docs/agents/`:

- `issue-tracker.md` — based on the user's tracking preference
- `triage-labels.md` — the five canonical roles mapped to their tracker's labels
- `domain.md` — single-context or multi-context layout

Use the user's issue tracker choice to pick the right template. If they chose GitHub, document `gh` CLI usage. If local markdown, document the `.scratch/` convention.

### Phase 4: Verification & Handoff

1. **Validate** — check that:
   - Skills directories exist under `.agents/skills/` with valid `SKILL.md` files
   - AGENTS.md exists and is non-empty
   - Any CLAUDE.md / cursor rules were written
   - No broken references in the plan

2. **Summarize** — tell the user:
   - How many skills were installed (catalog + marketplace + custom)
   - Where AGENTS.md was written
   - Which harness-specific files were derived
   - Which extensions/MCP servers were configured

3. **Next steps** — suggest:
   - Run `/setup-matt-pocock-skills` (if engineering domain with Matt Pocock skills installed) to configure issue tracker
   - Try an initial prompt relevant to their domain
   - Re-run `/bootstrap` to add another domain

## Re-running for cross-domain composition

When `/bootstrap` is run again on a project that already has an AGENTS.md:

1. In Phase 1, note that this is an additional domain, not the first setup
2. In Phase 2, include the existing skills in the gap analysis (don't re-recommend installed skills)
3. In Phase 3:
   - Skills: `npx skills add` is idempotent (skips duplicates)
   - AGENTS.md: append new sections, never overwrite existing
   - CLAUDE.md: regenerate entirely from updated AGENTS.md
   - State machines: add the new domain's state machine as an additional section

## Reference guides

- [domain-catalog.md](references/domain-catalog.md) — how to read and use the domain catalog
- [skill-sources.md](references/skill-sources.md) — known skill sources and search patterns
- [cross-harness.md](references/cross-harness.md) — harness compatibility and extension mapping
- [custom-skills.md](references/custom-skills.md) — principles for generating custom skills
