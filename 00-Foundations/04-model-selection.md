# Model Selection

← [How Claude Thinks](./03-how-claude-thinks.md) · [→ Domain 1: Agentic Architecture](../01-Agentic-Architecture/README.md)

---

## Core concept

> Anthropic's Claude model family offers a tiered trade-off between speed, cost, and capability. **Model selection is about matching the model tier to the workflow's latency and quality requirements** — not always picking the most powerful model.

---

## The model tiers

| Tier | Models | Speed | Cost | Capability |
|------|--------|-------|------|-----------|
| **Haiku** | claude-haiku-* | Fastest | Cheapest | Good for structured tasks, classification, simple retrieval |
| **Sonnet** | claude-sonnet-* | Balanced | Mid-range | Most production workloads; strong reasoning + tool use |
| **Opus** | claude-opus-* | Slowest | Most expensive | Complex multi-step reasoning, nuanced judgment |

---

## When to use each tier

### Haiku — use for:
- Blocking pre-merge CI checks (speed matters more than depth)
- High-volume, latency-sensitive automation (1000s of requests/hour)
- Simple classification or extraction tasks
- Background tasks where cost efficiency is the primary concern

### Sonnet — use for:
- Most agentic workflows (customer support agents, research systems)
- Code generation and review with moderate complexity
- Balanced quality/cost in production

### Opus — use for:
- Deep nightly/weekly analysis where quality > speed
- Complex architectural reasoning
- Tasks where misses have high cost (legal, medical, financial)
- Coordinator agent in a multi-agent system where judgment is critical

---

## Exam patterns

The exam rarely asks "which model should I use?" directly. Instead, it frames scenarios where changing the model is a **tempting but wrong** answer.

### ⚠️ Exam traps

| Scenario | Tempting answer | Correct answer |
|----------|----------------|----------------|
| Pre-merge CI check takes 8-12 minutes, frustrating developers | Switch to Haiku | Create a **focused prompt** covering only blocking issues (security, breaking changes); reserve deep analysis for async post-merge |
| Code review produces inconsistent output quality | Upgrade to Opus | Fix the prompt: add explicit criteria, structured output format, and few-shot examples |
| Synthesis agent produces shallow output when subagents time out | Use a bigger model | Fix the **synthesis agent's prompt** to handle partial inputs gracefully |
| High false positive rate in complexity checks | Upgrade model | **Separate the review into distinct passes** per check type; disable the high-FP category temporarily |

> **Key rule**: The correct fix is almost always prompt redesign, workflow restructuring, or explicit criteria — not a model upgrade. Model upgrades add cost without addressing the root cause.

---

## Multi-agent model strategy

In multi-agent systems, you can use different models for different roles:

```
Coordinator Agent  → Sonnet (needs good judgment for routing decisions)
Web Search Subagent → Haiku (structured retrieval, low reasoning needed)
Doc Analysis Subagent → Haiku or Sonnet (depends on document complexity)
Synthesis Subagent → Sonnet or Opus (needs to reconcile conflicting sources)
Report Gen Subagent → Haiku (formatting task, low reasoning)
```

This hybrid approach optimises cost without sacrificing quality where it matters.

---

## Cost considerations for the exam

| API | Cost | Latency SLA | Best for |
|-----|------|-------------|----------|
| Standard (synchronous) | Full price | Immediate | Blocking workflows, interactive chat |
| Message Batches API | **50% off** | Up to 24 hours (no guarantee) | Non-blocking batch jobs |

> Batch API is for latency-tolerant workloads only — nightly reports, weekly audits, async test generation. Never use it for blocking pre-merge checks.

---

## Related topics

- [Tokens & Context Window](./01-tokens-and-context-window.md) — cost grows with context size
- [Batch Processing](../04-Prompt-Engineering/05-batch-processing.md) — the 50% cost reduction option
- [CI/CD Integration](../03-Claude-Code-Configuration/06-cicd-integration.md) — choosing the right model for pipeline stages
