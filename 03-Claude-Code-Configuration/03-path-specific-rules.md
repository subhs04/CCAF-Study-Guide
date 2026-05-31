# Path-Specific Rules

← [Custom Skills & Commands](./02-custom-skills-and-commands.md) · [→ Next: Plan Mode](./04-plan-mode.md)

---

## Core concept

> Path-specific rules are rule files in `.claude/rules/` that activate only when Claude is editing files matching a specified glob pattern. They reduce token usage (irrelevant rules don't load) and are the right solution when conventions apply to a file type spread across multiple directories.

---

## Structure

```
.claude/
└── rules/
    ├── terraform.md         # loads for infrastructure files
    ├── testing.md           # loads for all test files
    ├── api-conventions.md   # loads for API route files
    └── migrations.md        # loads for database migration files
```

Each file has YAML frontmatter specifying which paths activate it:

```markdown
---
paths:
  - "**/*.test.tsx"
  - "**/*.spec.ts"
  - "tests/**/*"
---

# Testing conventions

- Use describe/it blocks for test grouping
- Mock external dependencies with jest.mock()
- Test file should import the module it tests directly, not via index
- Each test should have exactly one assertion (or use expect.assertions(n))
```

This rule file loads only when Claude is editing test files — saving tokens for every other task.

---

## Glob patterns

| Pattern | Matches |
|---------|---------|
| `**/*.test.tsx` | Any .test.tsx file anywhere in the project |
| `terraform/**/*` | Any file inside the terraform directory |
| `src/api/**/*.ts` | TypeScript files inside src/api and subdirectories |
| `*.config.js` | Config files in the project root only |
| `**/*.{ts,tsx}` | All TypeScript and TSX files |

---

## Path-specific rules vs directory CLAUDE.md

Both accomplish conditional rule loading, but they're suited to different situations:

```
Scenario 1: React component conventions
→ All components are in src/components/
→ Use: src/components/CLAUDE.md (directory-level)
   → Auto-loads when working in that directory

Scenario 2: Test file conventions
→ Test files are scattered: src/__tests__/, components/__tests__/, api/__tests__/
→ Use: .claude/rules/testing.md with paths: ["**/*.test.tsx"]
   → Loads for any test file regardless of location
```

**Rule**: If the convention applies to a directory → directory CLAUDE.md. If it applies to a file type that's spread across the codebase → `.claude/rules/` with glob paths.

---

## Benefits

- **Token efficiency**: Only load rules when they're relevant to the current file
- **No config bloat**: Keep root CLAUDE.md concise; detailed conventions live in rule files
- **Codebase-wide file types**: Handles test files, config files, migration files cleanly regardless of directory location
- **Easy to add**: New convention for a file type = new rule file + glob pattern; no editing root CLAUDE.md

---

## ⚠️ Exam traps

| Wrong answer | Correct answer |
|-------------|---------------|
| "Create a testing/ directory CLAUDE.md for test conventions" | Tests are spread across directories → `.claude/rules/testing.md` with glob |
| "Add test conventions to root CLAUDE.md" | They'll load for every task, not just test editing — use path-specific rules |
| "Use directory CLAUDE.md for Terraform rules since Terraform files are in one folder" | Either works; directory CLAUDE.md is fine here; .claude/rules/ also fine |
| "Path-specific rules replace CLAUDE.md entirely" | They supplement CLAUDE.md — use both for different purposes |

---

## Related topics

- [CLAUDE.md Hierarchy](./01-claudemd-hierarchy.md) — how path rules fit into the overall hierarchy
- [Custom Skills & Commands](./02-custom-skills-and-commands.md) — skills for task-specific workflows
