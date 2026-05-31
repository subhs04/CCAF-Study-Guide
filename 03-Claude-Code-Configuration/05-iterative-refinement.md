# Iterative Refinement Techniques

← [Plan Mode](./04-plan-mode.md) · [→ Next: CI/CD Integration](./06-cicd-integration.md)

---

## Core concept

> When Claude's output doesn't match expectations, the most effective fix is almost always **providing concrete examples** of what you want — not writing more detailed natural language instructions. Show, don't tell. This principle applies to transformation tasks, skill consistency, code generation, and anywhere output is "close but not quite right."

---

## Technique 1: Concrete input-output examples

**When to use**: Claude consistently misinterprets transformation requirements despite clear instructions.

```
❌ Description-only approach:
"Transform API responses into normalised format with camelCase fields,
ISO 8601 timestamps, and nested address objects."
→ Claude interprets differently each time

✅ Example-based approach:
Input:
{
  "user_id": 123,
  "created_ts": 1717200000,
  "addr_line1": "123 Main St",
  "addr_city": "Boston"
}

Expected output:
{
  "userId": 123,
  "createdAt": "2024-06-01T10:00:00Z",
  "address": {
    "line1": "123 Main St",
    "city": "Boston"
  }
}
```

Provide 2–3 examples covering representative cases including edge cases (null values, nested structures, unusual formats).

---

## Technique 2: Test-driven iteration

Write tests first, then iterate by sharing failures.

```mermaid
flowchart LR
    A[Write test suite\ncovering expected behavior\nedge cases\nperformance requirements] --> B[Ask Claude to\nimplement the function]
    B --> C[Run tests]
    C -->|Failures| D[Share failing tests\nwith Claude:\n"Tests 3 and 7 fail:\n{error output}"]
    D --> E[Claude revises\nimplementation]
    E --> C
    C -->|All pass| F[Done ✓]
```

**Why it works**: Test failures give Claude specific, unambiguous feedback. "Test 3 expects null for missing field but got 'undefined'" is more actionable than "it doesn't handle null correctly."

---

## Technique 3: The interview pattern

For unfamiliar domains, have Claude ask questions before implementing:

```
Prompt: "Before implementing the caching layer, ask me any questions
you need to make good design decisions. Consider: cache invalidation strategy,
failure modes, consistency requirements, and performance targets."

Claude asks:
1. "Should the cache be write-through or write-behind?"
2. "What should happen if the cache is unavailable — fail or fall through to DB?"
3. "Is eventual consistency acceptable or do you need strong consistency?"

→ After you answer, Claude implements with full context
```

**When to use**: Unfamiliar domains, complex systems with many valid approaches, where Claude's default assumptions may not match your requirements.

---

## Technique 4: Independent vs interacting issues

**Independent issues** (fix one at a time):
```
"The function doesn't handle null input" → fix → test → done
"The timestamp format is wrong" → fix separately
```

**Interacting issues** (fix together in one message):
```
"The null check and the timestamp formatting both affect the same
branch of the code — fix them together: [issue 1 + issue 2 + context]"
```

Sending interacting issues separately causes Claude to fix one and inadvertently break the other.

---

## When instructions do work better than examples

Examples are most powerful for **format and structure** consistency. Instructions work fine for:
- Simple constraints: "Return only valid JSON, no prose"
- Scope limits: "Focus only on changed lines, not the whole file"
- Explicit exclusions: "Skip style issues; only flag logic errors"

For complex output formatting and transformation tasks → always examples.

---

## ⚠️ Exam traps

| Wrong answer | Correct answer |
|-------------|---------------|
| "Rewrite requirements with greater technical precision" | Provide **concrete input-output examples** — precision in language rarely helps as much as examples |
| "Ask Claude to explain its interpretation to find divergence" | Provide examples showing the correct transformation directly |
| "Add more detailed instructions to SKILL.md for consistency" | Add 3–5 concrete example outputs to SKILL.md |
| "Fix independent and interacting bugs in separate messages" | Fix interacting issues together in one message; fix independent issues separately |

---

## Related topics

- [Few-Shot Prompting](../04-Prompt-Engineering/02-few-shot-prompting.md) — same principle applied to prompt engineering broadly
- [Custom Skills & Commands](./02-custom-skills-and-commands.md) — examples in SKILL.md
- [CI/CD Integration](./06-cicd-integration.md) — structured output for automated pipelines
