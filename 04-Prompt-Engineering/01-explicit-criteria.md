# Explicit Criteria & Reducing False Positives

← [Domain 4 index](./README.md) · [→ Next: Few-Shot Prompting](./02-few-shot-prompting.md)

---

## Core concept

> Vague review instructions ("be conservative", "only report high-confidence findings") produce inconsistently low false positive rates. What works is replacing vague guidance with **explicit categorical criteria** — telling Claude precisely which categories of issues to report and which to skip. This is especially important when high false-positive categories are undermining developer trust in all review output.

---

## The false positive problem

When automated code review flags too many non-issues, developers stop reading the findings:

```
Week 1: Review produces 15 findings per PR
Week 4: Developers start dismissing findings without reading
Week 8: A real security vulnerability is ignored because it looks like noise
```

The fix is not to add more instructions like "be conservative" — that doesn't reliably reduce false positives for specific categories. The fix is to define exactly what to flag.

---

## Vague vs explicit criteria

```
❌ Vague:
"Review this code for quality issues. Be thorough but conservative.
Only report high-confidence findings."

→ "High-confidence" is interpreted inconsistently
→ 40% false positive rate in complexity checks
→ Developers lose trust

✅ Explicit:
"Review for the following issues ONLY:

REPORT:
1. Security vulnerabilities: SQL injection, XSS, exposed credentials,
   authentication bypass, insecure direct object reference
2. Breaking changes: API signature changes, removed public methods,
   changed return types
3. Critical bugs: null pointer dereferences, infinite loops,
   incorrect concurrency patterns

SKIP (do not report):
- Style issues (naming conventions, formatting) — handled by linter
- Minor improvements ("consider using X instead of Y")
- Documentation gaps
- Performance optimisations (unless severe — O(n²) on large datasets)
- Local patterns established in this module (even if different from global conventions)"
```

---

## Consistent severity ratings

Without explicit severity criteria, similar issues get rated Critical in one PR and Minor in another. Fix with a severity rubric:

```
SEVERITY LEVELS:

CRITICAL — would cause data loss, security breach, or system outage
  Example: unparameterised SQL query accepting user input

HIGH — functional bug in critical path
  Example: incorrect null check causing NullPointerException in payment flow

MEDIUM — functional issue in non-critical path, or security issue with limited scope
  Example: race condition in notification service

LOW — improvement opportunity with no current functional impact
  Example: missing input validation for optional admin-only field

[Include one concrete code example for each severity level to calibrate the model]
```

---

## Handling categories with high false positive rates separately

When one review category (e.g., complexity checks) has a 40% false positive rate while others (e.g., security) have 5%:

```
❌ Run everything together:
"Check security, complexity, and style issues"
→ Developer sees 15 findings, 6 of which are wrong → discards all 15

✅ Separate into distinct review passes:
Pass 1: Security only (5% FP rate → high trust)
Pass 2: Complexity (40% FP rate → currently disabled while improving)

Disable high-FP categories temporarily.
Improve the complexity prompt separately.
Re-enable when FP rate drops.
```

---

## ⚠️ Exam traps

| Wrong answer | Correct answer |
|-------------|---------------|
| "Add 'be conservative' to the review prompt" | Add explicit categorical criteria — what to flag vs skip |
| "Combine security and complexity into one pass with stricter criteria" | Separate into distinct passes; disable the high-FP category |
| "Add a confidence threshold that filters out low-confidence findings" | Define categorical criteria — confidence filtering doesn't fix the root cause |
| "Upgrade to a more capable model for better precision" | Fix the prompt criteria first; model upgrades don't solve prompt vagueness |

---

## Related topics

- [Few-Shot Prompting](./02-few-shot-prompting.md) — few-shot examples reinforce explicit criteria with concrete demonstrations
- [CI/CD Integration](../03-Claude-Code-Configuration/06-cicd-integration.md) — applying explicit criteria in automated pipelines
- [Multi-Pass Review](./06-multi-pass-review.md) — separating review types into distinct passes
