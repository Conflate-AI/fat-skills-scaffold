# Cross-Harness Compatibility Reference

## Harness overview

| Harness | Skills location | Context file | Extensions/tools | Install command |
|---|---|---|---|---|
| **Pi** | `.pi/skills/`, `~/.pi/agent/skills/`, `.agents/skills/` | `AGENTS.md` | Pi packages (git/npm), `.ts` extensions, `pi-mcp-adapter` | `pi install <source>` |
| **Claude Code** | `.claude/skills/`, `.agents/skills/` | `CLAUDE.md` | MCP servers (`.mcp.json`, `~/.claude.json`) | Add to `.mcp.json` |
| **Cursor** | `.cursor/rules/`, `.agents/skills/` | `.cursor/rules/*.md` | MCP servers (native config) | Add to MCP config |
| **Codex CLI** | `.agents/skills/` | `AGENTS.md` | Built-in tools | N/A |
| **Gemini CLI** | `.agents/skills/` | `AGENTS.md` | MCP servers | Add to config |
| **AiderDesk** | `.aiderdesk/skills/`, `.agents/skills/` | varies | varies | varies |

## How `npx skills add` handles cross-harness

When you run `npx skills add <owner/repo@skill> --yes`:

1. Clones the source repo
2. Copies the skill to `.agents/skills/<skill-name>/` (universal location)
3. Creates symlinks to harness-specific directories (`.claude/skills/`, `.pi/skills/`, etc.)
4. Detects installed harnesses automatically

**Result**: A single `npx skills add` command makes the skill available in all installed harnesses. This is the primary installation mechanism — do not manually copy skills to harness-specific directories.

## Context file strategy

### Primary: AGENTS.md

AGENTS.md is the open standard, read by Pi, Codex, Cursor, Aider, and other multi-agent tools. It's the source of truth.

Write to: `<project_directory>/AGENTS.md`

### Derived: CLAUDE.md

If the user selected Claude Code as a harness, derive CLAUDE.md from AGENTS.md:

- Full copy of AGENTS.md content
- Prepend: `<!-- Derived from AGENTS.md by fat-skills-scaffold. Edit AGENTS.md and re-run /bootstrap to update. -->`
- On re-runs, regenerate CLAUDE.md entirely (it's a derived artifact, not source-of-truth)
- Write to: `<project_directory>/CLAUDE.md`

### Derived: .cursor/rules/

If the user selected Cursor as a harness:

- Write `.cursor/rules/fat-skills-scaffold.md` with the AGENTS.md content
- Prepend the same derivation comment
- Cursor's directory form (`.cursor/rules/*.md`) is preferred over legacy `.cursorrules`
- On re-runs, regenerate the file entirely
- Write to: `<project_directory>/.cursor/rules/fat-skills-scaffold.md`

## Extension mapping by capability

Different harnesses have different extension ecosystems for the same capabilities. Map by what the user needs:

| Capability | Pi | Claude Code | Cursor | Generic MCP |
|---|---|---|---|---|
| Code intelligence (AST) | Pi-Astrolabe | — | — | `typescript-mcp-server` |
| Subagent delegation | Pi-Simple-Subagents | — | — | — |
| Web search | pi-web-access (pkg) | — | — | brave-search-mcp |
| Browser automation | — | browser-mcp | playwright-mcp | playwright-mcp |
| Database access | — | postgres-mcp | postgres-mcp | postgres-mcp |
| Financial data | — | alpha-vantage-mcp | alpha-vantage-mcp | alpha-vantage-mcp |
| Generic tool bridge | pi-mcp-adapter | native MCP | native MCP | — |

## MCP server configuration

For harnesses that use MCP (Claude Code, Cursor, Gemini CLI), write to `.mcp.json` in the project root:

```json
{
  "mcpServers": {
    "<server-name>": {
      "command": "npx",
      "args": ["-y", "<package-name>"],
      "lifecycle": "lazy"
    }
  }
}
```

- Use `"lifecycle": "lazy"` by default — servers only start when needed
- If `.mcp.json` already exists, merge new servers in (don't overwrite existing)
- Each catalog domain can specify MCP servers under `extensions.claude-code.mcp-servers` and `extensions.cursor.mcp-servers`

## Pi-specific setup

Pi is the first-class harness. Beyond the universal setup:

1. **Pi packages** — install via `pi install <source>`
2. **pi-mcp-adapter** — if MCP servers are needed, recommend installing this package. It gives Pi access to MCP servers without burning the context window (one proxy tool instead of hundreds of tool definitions).
3. **Pi settings** — if the project needs specific Pi settings, suggest adding them to `.pi/settings.json`
4. **Pi skills** — `npx skills add` handles symlinks to `.pi/skills/` automatically; no manual setup needed
