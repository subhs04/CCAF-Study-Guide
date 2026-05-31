# Few-Shot Prompting

← [Explicit Criteria](./01-explicit-criteria.md) · [→ Next: Structured Output & JSON Schemas](./03-structured-output-json-schemas.md)

---

## Core concept

> **Few-shot prompting** means including 2–6 examples in your prompt that demonstrate the exact behaviour you want. It is the most reliable technique for achieving consistent output when detailed instructions alone fail. Examples are especially powerful for showing how to handle ambiguous cases — ones where the right choice isn't obvious from a rule alone.

---

## Why examples outperform instructions

Instructions describe what to do in words. Examples show it:

```
❌ Instruction: "Generate commit messages in imperative mood, referencing the ticket,
under 72 characters, with type prefix."

✅ Instruction + examples:
"Generate commit messages following these examples:

feat(auth): add MFA support for enterprise users (#PROJ-234)
fix(payments): prevent double-charge on retry logic (#PROJ-198)
refactor(api): extract rate limiting into middleware (#PROJ-201)
docs(setup): update Docker installation steps for M2 Macs
chore(deps): upgrade Express to 4.19.2 for security patch (#PROJ-210)"
```

The examples make implicit rules explicit:
- `type(scope):` prefix format
- Imperative verb (add, prevent, extract, update, upgrade)
- Ticket reference in parentheses when applicable
- No ticket for docs and chore commits
- All under 72 characters

---

## Targeting ambiguous cases

Few-shot examples are most valuable for the cases instructions fail to resolve — where two plausible actions exist and you want Claude to consistently choose one.

**Tool selection ambiguity**:
```
User says: "I need help with my recent purchase"
→ Is this get_customer or lookup_order?

Example in system prompt:
Query: "I need help with my recent purchase"
Reasoning: "User mentions a purchase but no order number — likely needs account context first"
Action: get_customer

Query: "check my order #55123"
Reasoning: "Specific order ID provided — direct order lookup"
Action: lookup_order

Query: "something's wrong with what I bought last week"
Reasoning: "Vague, no order ID — need customer context to identify the right order"
Action: get_customer
```

4–6 ambiguous-case examples build the model's judgment for novel ambiguous queries it hasn't seen before.

---

## Format consistency

When you need output in a specific format, show the exact format in examples:

```
❌ "For each finding, include the file, line number, issue, and a suggested fix."

✅ "Format each finding exactly like this:

[FILE: src/payments/refund.py] [LINE: 47] [SEVERITY: HIGH]
Issue: Missing null check on customer_id before calling process_refund
Fix: Add `if not customer_id: raise ValueError('customer_id required')`

[FILE: src/auth/verify.py] [LINE: 123] [SEVERITY: CRITICAL]
Issue: User-controlled input concatenated directly into SQL query
Fix: Use parameterised query: `cursor.execute('SELECT * FROM users WHERE id = %s', (user_id,))`"
```

The parseable template format `[FILE: ...] [LINE: ...] [SEVERITY: ...]` is then used in CI to automatically post inline PR comments.

---

## How many examples?

| Situation | Examples needed |
|-----------|----------------|
| Simple, unambiguous format | 1–2 |
| Moderate ambiguity, clear pattern | 3–4 |
| High ambiguity, many edge cases | 4–6 |
| Extracting from varied document structures | 4–6, covering each document variation |

More than 6 examples rarely adds value and increases prompt size. Target the **most ambiguous** cases, not the obvious ones.

---

## Few-shot examples for extraction tasks

When extracting structured data from varied document formats (invoices, contracts, receipts), examples help Claude handle structural variation:

```
Example 1 — Invoice with inline citations:
Document: "The total amount due is $1,247.50 (see items below)..."
Extraction: { "total": 1247.50, "currency": "USD", "citation_style": "inline" }

Example 2 — Invoice with separate totals section:
Document: [items table]... [page 3] "Total: $892.00"
Extraction: { "total": 892.00, "currency": "USD", "citation_style": "separate_section" }

Example 3 — Invoice with unclear amount (use null, not fabricated value):
Document: "Invoice amount subject to currency conversion at time of payment"
Extraction: { "total": null, "currency": null, "note": "amount not determinable at invoice date" }
```

The third example prevents Claude from fabricating a value when the information is genuinely absent.

---

## ⚠️ Exam traps

| Wrong answer | Correct answer |
|-------------|---------------|
| "Add more detailed instructions for inconsistent output" | Add **concrete examples** — instructions alone rarely achieve format consistency |
| "Add 10–15 clear unambiguous examples for each tool" | Target 4–6 **ambiguous** cases with reasoning — unambiguous cases don't build generalisation |
| "Group all get_customer examples together, then all lookup_order examples" | Mix tools within examples; show disambiguation reasoning per case |
| "Fewer examples to keep the prompt shorter" | 4–6 targeted examples significantly outperform 1–2; keep prompt concise elsewhere |

---

## Related topics

- [Explicit Criteria](./01-explicit-criteria.md) — few-shot examples reinforce categorical criteria with demonstrations
- [Structured Output & JSON Schemas](./03-structured-output-json-schemas.md) — examples complement schema enforcement
- [Custom Skills & Commands](../03-Claude-Code-Configuration/02-custom-skills-and-commands.md) — adding examples to SKILL.md
