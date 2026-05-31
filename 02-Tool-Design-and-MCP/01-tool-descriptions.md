# Tool Descriptions

← [Domain 2 index](./README.md) · [→ Next: Structured Error Responses](./02-structured-error-responses.md)

---

## Core concept

> **Tool descriptions are the primary mechanism Claude uses to decide which tool to call.** Minimal, vague, or overlapping descriptions lead to misrouting — Claude calling `get_customer` when it should call `lookup_order`, or using a generic `fetch_url` tool for web searches instead of just document retrieval. The fix is almost always to rewrite the descriptions before adding routing layers or few-shot examples.

---

## What a good tool description includes

| Element | What it covers | Example |
|---------|---------------|---------|
| **Purpose** | What the tool does | "Retrieves order details including status, items, and shipping info" |
| **Input formats** | What identifiers/data it accepts | "Accepts order IDs (format: #12345 or 12345)" |
| **Example queries** | Concrete user requests that should trigger this tool | "Use when user asks about order status, shipping, delivery date" |
| **Edge cases** | Unusual inputs it handles | "Works with both numeric and hash-prefixed order IDs" |
| **Boundaries** | When NOT to use it | "Do NOT use for customer account information — use get_customer instead" |

---

## The misrouting problem — and how to fix it

**Before (causes misrouting):**
```json
{
  "name": "get_customer",
  "description": "Retrieves customer information"
}
{
  "name": "lookup_order",
  "description": "Retrieves order details"
}
```
→ Both descriptions are nearly identical in what they tell Claude. When a user says "check my recent purchase", Claude guesses.

**After (eliminates misrouting):**
```json
{
  "name": "get_customer",
  "description": "Retrieves customer account information by customer ID or email address. Use when you need the customer's name, account status, contact details, or verified customer ID. Use this FIRST before any order operations to verify identity. Do NOT use for order-specific information."
}
{
  "name": "lookup_order",
  "description": "Retrieves order details by order ID (#12345 format). Returns order status, items, shipping address, delivery date, and payment status. Use when the user asks about a specific order, shipment, or delivery. Requires a verified customer ID from get_customer for account security."
}
```

---

## Renaming to eliminate overlap

When two tools have functionally similar names, rename the one with the narrower scope to make the distinction obvious.

**Problem**: `analyze_content` (web search agent) vs `analyze_document` (doc analysis agent) — near-identical names cause 45% misrouting.

```
❌ analyze_content    ← could mean anything
❌ analyze_document   ← also vague

✅ extract_web_results     ← clearly for web search output
✅ analyze_uploaded_document ← clearly for user-provided documents
```

Rename to reflect the **input source** or **specific action**, not just the general category.

---

## Replacing generic tools with purpose-specific ones

Generic tools invite misuse. A document analysis agent with `fetch_url` access will start using it to search the web.

```
❌ fetch_url — accepts any URL, fetches any content
   → Document analysis agent uses it to run ad-hoc Google searches

✅ fetch_document — validates that the URL matches document patterns
   (PDFs, Word files, known document repositories)
   → Prevents web search; agent must route search requests through coordinator
```

---

## System prompt effects on tool selection

The words you use in the system prompt can inadvertently bias tool selection. If your system prompt says "analyse the user's problem", and a tool is named `analyze_content`, Claude may associate those keywords.

- Audit your system prompt for keywords that map to tool names
- Prefer action verbs that are specific: `retrieve`, `process`, `validate` over `analyze`, `handle`, `check`

---

## ⚠️ Exam traps

| Scenario | Wrong answer | Correct answer |
|----------|-------------|---------------|
| Agent calls wrong tool for order queries | Add pre-routing classifier that selects tool based on keywords | **Expand tool descriptions** with clear boundaries — this is the first fix |
| 45% misrouting between two agents with similar tools | Add few-shot examples to coordinator prompt | **Rename one tool** to eliminate name overlap; update descriptions |
| Document analysis agent runs web searches | Add prompt instruction not to search | **Replace fetch_url with fetch_document** — scoped tool prevents misuse |
| Too many tools degrade selection accuracy | Combine tools into one | **Scope tools per agent** — reduce the tool set each agent sees |

---

## Related topics

- [Tool Distribution & tool_choice](./03-tool-distribution-and-choice.md) — limiting which tools each agent sees
- [Subagent Context Passing](../01-Agentic-Architecture/03-subagent-context-passing.md) — scoping tools when spawning subagents
- [Few-Shot Prompting](../04-Prompt-Engineering/02-few-shot-prompting.md) — when few-shot examples complement (not replace) good descriptions
