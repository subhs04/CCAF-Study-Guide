# Context Management in Long Interactions

← [Domain 5 index](./README.md) · [→ Next: Escalation Patterns](./02-escalation-patterns.md)

---

## Core concept

> In long conversations and multi-step agent workflows, the context window fills up with information that was useful earlier but is now noise — detailed tool results, intermediate reasoning, raw API responses. Without active management, context bloat leads to slower responses, higher cost, and the "lost in the middle" effect where important information is overlooked.

---

## The "lost in the middle" effect

```
Context window (50,000 tokens):
[BEGINNING] System prompt, initial customer info   ← Strong attention
[MIDDLE]    Tool results from 8 prior lookups       ← Weaker attention
[MIDDLE]    Earlier turns in conversation           ← Weaker attention
[END]       Most recent turn, current question       ← Strong attention
```

**Implication**: Critical facts buried in the middle may be under-attended and effectively "lost". Place the most important information at the beginning or end.

---

## Tool results accumulate — trim them

In a customer support agent handling a complex return request, each tool call appends to context:

```
get_customer() → 40 fields returned (name, email, loyalty tier, preferences, 
                  account history, 12 addresses, billing info, 6 payment methods...)
lookup_order() → 35 fields returned (items, shipping, warehouse, carrier, 
                  tracking events, fulfillment details, internal codes...)
```

After 4 tool calls: ~300 tokens of relevant data buried in ~600 tokens of irrelevant data.

**Fix**: Trim tool results to only the fields relevant to the current task:

```python
def post_tool_use_hook(tool_name, result):
    if tool_name == "get_customer":
        # Keep only what's needed for support resolution
        return {
            "customer_id": result["customer_id"],
            "name": result["name"],
            "email": result["email"],
            "loyalty_tier": result["loyalty_tier"],
            "return_policy": result["return_policy_override"]
        }
    if tool_name == "lookup_order":
        return {
            "order_id": result["order_id"],
            "status": result["status"],
            "items": result["items"],
            "placed_date": result["placed_date"],
            "estimated_delivery": result["estimated_delivery"]
        }
```

---

## The "case facts" block for persistent critical information

In a multi-turn support conversation, critical facts (amounts, order numbers, customer promises) must not get lost as the conversation grows.

Maintain a structured "case facts" block that's included in every turn's prompt:

```python
system_prompt = f"""You are a customer support agent.

[CASE FACTS — always accurate, do not summarise these further]
Customer ID: {customer_id}
Order ID: {order_id}
Issue: Customer reported missing item (Product A) from order placed {order_date}
Refund amount discussed: ${refund_amount}
Customer's stated expectation: "{verbatim_customer_statement}"
Agent actions taken: [{actions_list}]
[END CASE FACTS]

[Conversation summary]
{summarised_history}

[Recent turns]
{recent_turns}
"""
```

The case facts block is **never summarised** — it contains exact numbers, dates, and verbatim statements that lose fidelity through summarisation.

---

## Progressive summarisation risks

When summarising long conversations to manage context, certain information types are frequently lost:

| Fragile information type | Example | Risk when summarised |
|-------------------------|---------|---------------------|
| Specific amounts | "Refund of $67.49" | Becomes "refund discussed" |
| Exact dates | "Ordered on 2026-05-15" | Becomes "ordered recently" |
| Verbatim customer statements | "I need this resolved today or I'm cancelling" | Dropped entirely |
| Intermediate commitments | "Agent promised callback by 3pm" | Forgotten in summary |

**Fix**: Extract transactional facts into the case facts block before summarisation. Summarise only the conversational flow — not the facts.

---

## Context management for upstream agents

Upstream agents that return verbose content cause downstream overflow:

```
❌ Web search agent returns: full HTML of 5 pages (85K tokens)
   → Synthesis agent context overflows immediately

✅ Web search agent returns structured summary:
{
  "search_query": "AI in creative industries 2025",
  "sources": [
    {
      "title": "AI Disruption in Hollywood",
      "url": "...",
      "key_facts": ["60% of VFX studios adopted AI tools", "15% workforce reduction projected"],
      "relevance": 0.92
    }
  ]
}
```

---

## ⚠️ Exam traps

| Wrong answer | Correct answer |
|-------------|---------------|
| "Summarise everything including amounts and dates to save context" | Extract critical facts to a preserved case facts block; only summarise conversational flow |
| "Upstream agents should return full document content for completeness" | Return structured summaries (key facts, citations, relevance scores) |
| "Tool results don't need trimming" | Trim tool results to only fields relevant to the current task via PostToolUse hook |

---

## Related topics

- [Agent SDK Hooks](../01-Agentic-Architecture/05-agent-sdk-hooks.md) — PostToolUse hook for result trimming
- [Large Codebase Exploration](./04-large-codebase-exploration.md) — managing context in extended sessions
- [Tokens & Context Window](../00-Foundations/01-tokens-and-context-window.md) — why context management matters
