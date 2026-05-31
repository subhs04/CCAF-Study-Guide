# Master Exam Traps List

All common wrong-answer patterns across every domain. Scan this before your exam.

← [Quick Reference index](./README.md)

---

## Domain 1 — Agentic Architecture

| ❌ Wrong answer pattern | ✅ Correct approach |
|------------------------|---------------------|
| Parse natural language to detect loop completion | Check `stop_reason === "end_turn"` |
| Iteration cap as primary loop termination | Cap is safety net; `end_turn` is the primary stop |
| Agent-to-agent direct communication | All routing through coordinator |
| Subagents inherit coordinator's conversation history | Subagents are stateless; pass context explicitly every time |
| Let parallel agents overlap, then coordinator deduplicates | Coordinator partitions scope upfront before delegating |
| Coordinator validates all docs before dispatching to subagent | Subagents handle local error recovery; only escalate what they can't resolve |
| Synthesis agent calls coordinator for every claim verification | Give synthesis a scoped `verify_claim` tool for inline verification |
| Add few-shot examples to fix tool call ordering | Programmatic prerequisite gate — not prompt-based |
| Enhance system prompt to make critical ordering mandatory | PostToolUse hook or prerequisite gate — prompts have non-zero failure rate |
| Add "ask if fully addressed" confirmation step | Self-critique step (agent evaluates own draft) |
| Always resume previous session for continuity | If prior tool results stale → start fresh with injected summary |

---

## Domain 2 — Tool Design & MCP

| ❌ Wrong answer pattern | ✅ Correct approach |
|------------------------|---------------------|
| Add pre-routing classifier for tool selection | Expand and clarify tool descriptions first |
| Consolidate overlapping tools into one general-purpose tool | Purpose-specific scoped tools with clear descriptions |
| Give agents access to all available tools | 4–5 tools per agent maximum |
| `tool_choice: "auto"` to guarantee tool is called | Use `"any"` to guarantee tool use |
| Report "0 results found" as a failure | "0 results" = successful query with no matches — different from access failure |
| Retry all errors automatically before reporting | Only retry transient errors; non-retryable errors escalate immediately |
| Generic "operation failed" for all error types | Structured error: category, retryable flag, what was attempted, partial results |
| Install GitHub MCP server globally with --global | Install in `.mcp.json` — global expands access unnecessarily |
| Hardcode API tokens in .mcp.json | Use `${ENV_VAR}` expansion |
| User-level `~/.claude.json` for team-shared tools | `.mcp.json` in project root — shared via git |
| Grep for file names | Glob for file names; Grep for file contents |
| Edit when the target text appears multiple times | Read + Write when Edit fails due to non-unique match |

---

## Domain 3 — Claude Code Configuration

| ❌ Wrong answer pattern | ✅ Correct approach |
|------------------------|---------------------|
| Split into directory CLAUDE.md files to reduce file size | Use `@import` — keeps root concise; imports load on demand |
| Put team-shared instructions in `~/.claude/CLAUDE.md` | `~/.claude/CLAUDE.md` is personal/user-level, NOT in git |
| Directory CLAUDE.md for test conventions across the codebase | `.claude/rules/` with `paths: ["**/*.test.tsx"]` glob |
| Add more detailed natural language instructions to SKILL.md | Add concrete example outputs to SKILL.md |
| Split into /explore-start and /explore-end skills | Add `context: fork` frontmatter |
| Enable plan mode after changes have already begun | Enable plan mode at the start |
| Switch pre-merge hook to Haiku to reduce review time | Narrow the prompt scope (blocking issues only) |
| "Add actionable feedback" instruction to review prompt | Structured output format: file, line, issue, suggested fix |
| Add keyword filter post-processing for duplicate tests | Include existing test file in context |
| Same Claude instance reviews its own generated code | Use an independent Claude instance |
| Run second review without context from first review | Pass prior findings; report only new/unresolved issues |

---

## Domain 4 — Prompt Engineering

| ❌ Wrong answer pattern | ✅ Correct approach |
|------------------------|---------------------|
| "Be conservative" or "only high-confidence findings" | Explicit categorical criteria: what to flag vs skip |
| Combine high and low FP categories in one review pass | Separate passes; disable high-FP categories temporarily |
| More detailed prose instructions for consistency | Add concrete input-output examples |
| Group all same-tool few-shot examples together | Mix tools; show ambiguous-case disambiguation reasoning |
| Use batch API for blocking pre-merge checks | Synchronous API for blocking; batch for overnight/weekly |
| Batch API for tool-calling workflows | Batch doesn't support multi-turn tool calling |
| "Ask Claude to respond in JSON" for reliable structured output | `tool_use` with JSON schema — eliminates syntax errors |
| `tool_choice: "auto"` when you need guaranteed tool use | `"any"` guarantees tool is called |
| Make all extraction schema fields required | Nullable fields for absent information — prevents fabrication |
| Retry with the same prompt when validation fails | Include specific errors in retry prompt |
| Retry when required information isn't in the document | Route to human review — retrying can't extract non-existent information |

---

## Domain 5 — Context & Reliability

| ❌ Wrong answer pattern | ✅ Correct approach |
|------------------------|---------------------|
| Summarise everything including amounts and dates | Extract facts to case facts block; summarise only conversational flow |
| Escalate based on detected customer sentiment | Explicit categorical triggers (human request, policy gap, no progress) |
| Confidence score < 7/10 as escalation trigger | Categorical triggers — confidence scores are poorly calibrated |
| Try to resolve before honouring human escalation request | Honour immediately; offer resolution once maximum |
| Select most recently active account when multiple matches | Ask for additional identifying identifiers |
| Escalate all "complex" cases | Resolve standard policy cases autonomously |
| Simple random sampling for quality monitoring | Stratified sampling by document type |
| Overall accuracy metric to decide on automation | Accuracy by document type AND field before automating |
| Same context handles generation and review | Independent review instance — self-review is biased |
| Report temporal difference as contradiction | Include publication dates; interpret temporally, not as conflict |
| Pick one conflicting source as authoritative | Document both with annotations; let coordinator decide |
| Continue same session through entire codebase exploration | Scratchpad files + subagents for verbose subtasks + /compact |

---

## The universal pattern that appears everywhere

> **Examples beat instructions.** In every domain, when the exam asks how to fix inconsistent or incorrect output, the correct answer almost always involves **concrete examples** — not more detailed instructions, not model upgrades, not post-processing filters.

This applies to:
- SKILL.md commit message formatting → add examples
- Tool selection ambiguity → add few-shot examples with reasoning
- Extraction from varied document formats → add examples covering each format type
- Code review output format → add structured example findings
- Transformation tasks → add 2-3 input-output examples

← [Quick Reference index](./README.md)
