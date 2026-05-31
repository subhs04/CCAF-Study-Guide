# Frontmatter Options Reference

← [Quick Reference index](./README.md)

---

## Skill (SKILL.md) frontmatter

```markdown
---
context: fork
allowed-tools: Write, Edit, Read
argument-hint: "<migration-name>"
---
```

| Key | Value | Effect |
|-----|-------|--------|
| `context` | `fork` | Run skill in isolated sub-agent; output doesn't affect main session context |
| `allowed-tools` | Comma-separated list | Restrict which tools the skill can call |
| `argument-hint` | `"<hint text>"` | Displayed as a prompt when skill invoked without arguments |

### When to use context: fork

Use whenever the skill produces verbose output that you don't want persisting in the main conversation:
- Codebase analysis
- Brainstorming alternatives  
- Any exploratory workflow

---

## .claude/rules/ frontmatter

```markdown
---
paths:
  - "**/*.test.tsx"
  - "terraform/**/*"
---
```

| Key | Value | Effect |
|-----|-------|--------|
| `paths` | Array of glob patterns | Rule file only loads when editing files matching these patterns |

---

## .mcp.json structure

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@scope/mcp-server"],
      "env": {
        "API_TOKEN": "${API_TOKEN}"
      }
    }
  }
}
```

| Field | Purpose |
|-------|---------|
| `command` | How to launch the MCP server |
| `args` | Arguments to the command |
| `env` | Environment variables — use `${VAR}` for values from shell environment |

**Location** → `.mcp.json` in project root = shared with team via git  
**Location** → `~/.claude.json` = personal, not shared

---

## CLAUDE.md @import syntax

```markdown
@import ./.claude/standards/typescript.md
@import ./.claude/standards/testing.md
```

Imports load the referenced file's contents in place. Use to keep root CLAUDE.md concise while referencing detailed standards.

---

## tool_choice options

```python
tool_choice = {"type": "auto"}                           # Default: may return text or call tool
tool_choice = {"type": "any"}                            # Must call a tool; Claude chooses which
tool_choice = {"type": "tool", "name": "extract_data"}  # Must call this specific tool
```

| Option | When Claude returns text | When to use |
|--------|------------------------|------------|
| `auto` | Yes — if it doesn't think a tool is needed | Most conversational use |
| `any` | No — must call a tool | Guarantee structured output; multiple valid schemas |
| `{"type":"tool","name":"..."}` | No — must call this specific tool | Force prerequisite step |

← [Quick Reference index](./README.md)
