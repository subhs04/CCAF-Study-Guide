# Tokens & Context Window

← [Foundations](./README.md) · [→ Next: RAG & Retrieval](./02-rag-and-retrieval.md)

---

## Core concept

> A **token** is the basic unit Claude reads and writes — roughly ¾ of a word. The **context window** is the maximum number of tokens Claude can hold in "working memory" at once, covering everything: system prompt, conversation history, tool results, and the response being generated. Anything outside the window is invisible to the model.

---

## How tokenisation works

- Text is split into tokens by a tokeniser before being sent to the model.
- Common words = 1 token (`"the"`, `"is"`).
- Rare words, code, and non-English text = multiple tokens per word.
- A rough rule: **1,000 tokens ≈ 750 words ≈ 1.5 pages of text**.

```
"Claude Certified Architect" → ["Claude", " Certified", " Architect"] → 3 tokens
"Ärgernis" (German) → ["Ä", "rger", "nis"] → 3 tokens (more than English equivalent)
```

---

## Context window breakdown

```
┌──────────────────────────────────────┐
│           Context Window              │
│                                      │
│  System prompt                       │
│  Conversation history (all turns)    │
│  Tool results (accumulate each loop) │
│  ─────────────────────────────────── │
│  Current response (being generated)  │
└──────────────────────────────────────┘
```

Key facts:
- **Input tokens + output tokens together** must stay within the window.
- Tool results are appended to context on every agentic loop iteration — they accumulate fast.
- Exceeding the window: content is truncated or the API errors. Older content is dropped first.

---

## Why context management matters for the exam

The exam tests your ability to design systems that don't overflow the context window. Common patterns that cause overflow:

| Problem | Cause |
|---------|-------|
| Synthesis agent fails on large inputs | Upstream agents return full HTML/PDFs instead of structured summaries |
| Agentic loop slows and errors | Tool results (40+ fields per API call) accumulate without trimming |
| Multi-agent outputs degrade mid-conversation | No summarisation strategy; all prior tool calls remain in context |

**Fix pattern**: Modify upstream agents to return structured data (key facts, citations, relevance scores) instead of full content. Only keep fields relevant to the next step.

---

## The "lost in the middle" effect

Models reliably attend to content at the **beginning** and **end** of the context window. Content in the middle receives less attention and may be skipped during generation.

**Implication for design:**
- Put the most important information (system instructions, critical facts) at the start.
- Put the most recent / action-relevant content at the end.
- Don't bury critical findings in the middle of a 50-document batch.

---

## Token cost and model selection

| Model | Speed | Cost | Best for |
|-------|-------|------|----------|
| Haiku | Fastest | Cheapest | Blocking pre-merge checks, high-volume automation |
| Sonnet | Balanced | Mid | Most production workloads |
| Opus | Slowest | Most expensive | Deep analysis, complex reasoning |

> **Exam trap ⚠️**: Switching to a faster/cheaper model does NOT fix a slow or inaccurate review. The correct fix is almost always **narrowing the prompt scope** or **restructuring the workflow**, not model substitution.

---

## Related topics

- [RAG & Retrieval](./02-rag-and-retrieval.md) — how to fetch content without bloating the context
- [Context Management](../05-Context-and-Reliability/01-context-management.md) — strategies for long interactions
- [CI/CD Integration](../03-Claude-Code-Configuration/06-cicd-integration.md) — context in automated pipelines
