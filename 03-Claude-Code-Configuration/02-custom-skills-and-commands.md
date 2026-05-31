# Custom Skills & Slash Commands

← [CLAUDE.md Hierarchy](./01-claudemd-hierarchy.md) · [→ Next: Path-Specific Rules](./03-path-specific-rules.md)

---

## Core concept

> **Skills** (slash commands) are on-demand workflows that developers invoke explicitly. Unlike CLAUDE.md (which always loads), skills activate only when called. They live in SKILL.md files and support frontmatter configuration that controls isolation, tool access, and parameter handling.

---

## Locations

| Location | Scope | Shared? | Use for |
|----------|-------|---------|---------|
| `.claude/skills/<name>/SKILL.md` | Project | ✅ Yes (git) | Team-shared workflows |
| `~/.claude/skills/<name>/SKILL.md` | User | ❌ Personal | Personal skill variants |
| `.claude/commands/<name>.md` | Project | ✅ Yes (git) | Simpler team slash commands |
| `~/.claude/commands/<name>.md` | User | ❌ Personal | Personal slash commands |

---

## SKILL.md frontmatter options

```markdown
---
context: fork
allowed-tools: Write, Edit
argument-hint: "<migration-name>"
---

# Database migration generator

Creates a new migration file for the given name...
```

| Frontmatter key | What it does | When to use |
|----------------|-------------|------------|
| `context: fork` | Runs skill in an isolated sub-agent context; output does NOT affect main session | Verbose/exploratory skills; brainstorming; codebase analysis |
| `allowed-tools` | Restricts which tools this skill can call | Prevent destructive actions; limit scope |
| `argument-hint` | Displays a prompt for required params when skill invoked without arguments | Required parameters like migration name, file path, PR number |

---

## context: fork — why it matters

Without `context: fork`, the skill's output (which may be verbose) accumulates in the main conversation context. This:
- Slows subsequent responses (more tokens to process)
- Causes Claude to reference/be influenced by exploration context in future turns
- Can confuse implementation work that follows brainstorming

```
❌ /explore-alternatives runs without context: fork
   → 500 lines of exploration discussion remain in context
   → Claude references "abandoned approach #3" in the next implementation task

✅ context: fork isolates the skill run
   → Exploration happens in a forked sub-agent
   → Only a summary (or nothing) returns to main session
   → Main conversation context is clean
```

**Use context: fork for:**
- Codebase analysis (`/analyze-codebase`)
- Brainstorming alternatives (`/explore-approaches`)
- Any skill that produces verbose output not needed later

---

## $ARGUMENTS — passing parameters

`$ARGUMENTS` is replaced by whatever the developer types after the slash command:

```
/migration add_user_email_column
→ SKILL.md sees: $ARGUMENTS = "add_user_email_column"
```

Combined with `argument-hint`, this creates a guided parameter experience:

```markdown
---
argument-hint: "<migration-name>"
---
Generate a migration file named: $ARGUMENTS
```

→ If invoked without arguments, Claude prompts: "Please provide a migration name"

---

## Concrete examples > instructions in SKILL.md

When a skill produces inconsistent output, the fix is **adding concrete examples** to SKILL.md — not adding more natural language rules.

```markdown
❌ SKILL.md instruction:
"Generate commit messages that are concise, descriptive, use imperative mood,
reference the ticket, and clearly describe the change."
→ Still inconsistent for complex changes

✅ SKILL.md with examples:
"Generate commit messages in this format:

Examples:
feat(auth): add MFA support for enterprise users (#PROJ-234)
fix(payments): prevent double-charge on retry logic (#PROJ-198)
refactor(api): extract rate limiting into middleware (#PROJ-201)
docs(setup): update Docker installation steps for M2 Macs"
```

The examples demonstrate format, tone, and content better than any instruction can.

---

## Skills vs CLAUDE.md — when to use each

| CLAUDE.md | Skills |
|-----------|-------|
| Always-on context (coding standards, conventions) | On-demand workflows (generate migration, review PR, create component) |
| Universal rules that apply to every task | Task-specific procedures |
| Short team-wide guidelines | Complex multi-step procedures |

---

## ⚠️ Exam traps

| Wrong answer | Correct answer |
|-------------|---------------|
| "Add more detailed instructions to fix inconsistent skill output" | Add **concrete example outputs** to SKILL.md |
| "Split into /explore-start and /explore-end to manage context" | Add `context: fork` to frontmatter — designed for this exact problem |
| "Use allowed-tools: [] to prevent all tool use" | Specify the tools the skill IS allowed to use (positive list) |
| "The skill ran with tools it shouldn't have access to" | Add `allowed-tools` frontmatter to restrict tool access |

---

## Related topics

- [CLAUDE.md Hierarchy](./01-claudemd-hierarchy.md) — skills complement CLAUDE.md (on-demand vs always-on)
- [MCP Server Integration](../02-Tool-Design-and-MCP/04-mcp-server-integration.md) — MCP tools available to skills
- [Few-Shot Prompting](../04-Prompt-Engineering/02-few-shot-prompting.md) — same "examples > instructions" principle applies broadly
