# CCA-F Study Guide

**Claude Certified Architect – Foundations**  
Pass mark: **720 / 1000** · 60 questions · 4 scenarios drawn from 6 · No penalty for guessing

---

## How to use this guide

- Each section folder has its own `README.md` listing what's inside.
- Files are self-contained — you can read any one without reading the others.
- Mermaid diagrams render natively on GitHub.
- Exam traps are marked with ⚠️ in every file — scan those before your exam.

---

## Domain weightings

| Domain | Weight | Folder |
|--------|--------|--------|
| Agentic Architecture & Orchestration | **27%** | [01-Agentic-Architecture](./01-Agentic-Architecture/) |
| Claude Code Configuration & Workflows | **20%** | [03-Claude-Code-Configuration](./03-Claude-Code-Configuration/) |
| Prompt Engineering & Structured Output | **20%** | [04-Prompt-Engineering](./04-Prompt-Engineering/) |
| Tool Design & MCP Integration | **18%** | [02-Tool-Design-and-MCP](./02-Tool-Design-and-MCP/) |
| Context Management & Reliability | **15%** | [05-Context-and-Reliability](./05-Context-and-Reliability/) |

---

## All sections

### [00 · Foundations](./00-Foundations/)
Core concepts that underpin every domain. Not directly scored but essential for understanding.

- [Tokens & context window](./00-Foundations/01-tokens-and-context-window.md)
- [RAG & retrieval](./00-Foundations/02-rag-and-retrieval.md)
- [How Claude thinks](./00-Foundations/03-how-claude-thinks.md)
- [Model selection](./00-Foundations/04-model-selection.md)

### [01 · Agentic Architecture (27%)](./01-Agentic-Architecture/)

- [The agentic loop](./01-Agentic-Architecture/01-agentic-loop.md)
- [Multi-agent coordinator pattern](./01-Agentic-Architecture/02-multi-agent-coordinator.md)
- [Subagent context passing & spawning](./01-Agentic-Architecture/03-subagent-context-passing.md)
- [Multi-step workflows & enforcement](./01-Agentic-Architecture/04-multi-step-workflows.md)
- [Agent SDK hooks](./01-Agentic-Architecture/05-agent-sdk-hooks.md)
- [Task decomposition strategies](./01-Agentic-Architecture/06-task-decomposition.md)
- [Session state, resumption & forking](./01-Agentic-Architecture/07-session-state-and-forking.md)

### [02 · Tool Design & MCP (18%)](./02-Tool-Design-and-MCP/)

- [Tool descriptions](./02-Tool-Design-and-MCP/01-tool-descriptions.md)
- [Structured error responses](./02-Tool-Design-and-MCP/02-structured-error-responses.md)
- [Tool distribution & tool_choice](./02-Tool-Design-and-MCP/03-tool-distribution-and-choice.md)
- [MCP server integration](./02-Tool-Design-and-MCP/04-mcp-server-integration.md)
- [Built-in tools (Read / Write / Grep / Glob)](./02-Tool-Design-and-MCP/05-builtin-tools.md)

### [03 · Claude Code Configuration (20%)](./03-Claude-Code-Configuration/)

- [CLAUDE.md hierarchy & modular structure](./03-Claude-Code-Configuration/01-claudemd-hierarchy.md)
- [Custom skills & slash commands](./03-Claude-Code-Configuration/02-custom-skills-and-commands.md)
- [Path-specific rules](./03-Claude-Code-Configuration/03-path-specific-rules.md)
- [Plan mode vs direct execution](./03-Claude-Code-Configuration/04-plan-mode.md)
- [Iterative refinement techniques](./03-Claude-Code-Configuration/05-iterative-refinement.md)
- [CI/CD pipeline integration](./03-Claude-Code-Configuration/06-cicd-integration.md)

### [04 · Prompt Engineering (20%)](./04-Prompt-Engineering/)

- [Explicit criteria & reducing false positives](./04-Prompt-Engineering/01-explicit-criteria.md)
- [Few-shot prompting](./04-Prompt-Engineering/02-few-shot-prompting.md)
- [Structured output & JSON schemas](./04-Prompt-Engineering/03-structured-output-json-schemas.md)
- [Validation & retry loops](./04-Prompt-Engineering/04-validation-and-retry-loops.md)
- [Batch processing](./04-Prompt-Engineering/05-batch-processing.md)
- [Multi-pass & multi-instance review](./04-Prompt-Engineering/06-multi-pass-review.md)

### [05 · Context & Reliability (15%)](./05-Context-and-Reliability/)

- [Context management in long interactions](./05-Context-and-Reliability/01-context-management.md)
- [Escalation & ambiguity resolution](./05-Context-and-Reliability/02-escalation-patterns.md)
- [Error propagation across multi-agent systems](./05-Context-and-Reliability/03-error-propagation.md)
- [Large codebase exploration](./05-Context-and-Reliability/04-large-codebase-exploration.md)
- [Human review & confidence calibration](./05-Context-and-Reliability/05-human-review-and-confidence.md)
- [Information provenance & multi-source synthesis](./05-Context-and-Reliability/06-information-provenance.md)

### [Quick Reference](./Quick-Reference/)

- [Domain weights & exam format](./Quick-Reference/01-domain-weights-and-exam-format.md)
- [CLI flags](./Quick-Reference/02-cli-flags.md)
- [Frontmatter options](./Quick-Reference/03-frontmatter-options.md)
- [Master exam traps list](./Quick-Reference/04-exam-traps-masterlist.md)

---

## To push to GitHub

```bash
cd path/to/CCA-F-Study-Guide
git init
git add .
git commit -m "Initial CCA-F study guide"
gh repo create CCA-F-Study-Guide --public --source=. --push
```

> Tip: Install [GitHub CLI](https://cli.github.com/) (`gh`) for the one-liner above, or create the repo on github.com and follow the push instructions.
