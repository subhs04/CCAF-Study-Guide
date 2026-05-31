# Information Provenance & Multi-Source Synthesis

← [Human Review & Confidence](./05-human-review-and-confidence.md) · [→ Quick Reference](../Quick-Reference/README.md)

---

## Core concept

> In multi-agent research systems, source attribution is lost when findings pass through summarisation steps without explicit claim-source mappings. The synthesis agent receives findings from multiple subagents and has no way to trace which claim came from which source — unless that metadata was passed through every step. The solution is requiring subagents to output structured claim-source mappings that downstream agents are required to preserve.

---

## How attribution is lost

```
❌ What happens without structured attribution:

Web Search Agent output:
"Several studies show AI is reducing creative jobs by 15-20%"
[Source information discarded during summarisation]

Doc Analysis Agent output:
"Industry reports indicate 60% of studios adopted AI tools"
[Source information discarded]

Synthesis Agent receives:
- "AI is reducing creative jobs by 15-20%"
- "60% of studios adopted AI tools"
→ Synthesis can't cite sources; report contains uncited claims
```

```
✅ With structured attribution:

Web Search Agent output:
{
  "claim": "AI is reducing creative jobs by 15-20%",
  "source": {
    "url": "https://...",
    "title": "AI Impact Study 2025",
    "published": "2025-03-15",
    "excerpt": "...positions eliminated due to AI automation..."
  },
  "confidence": 0.85
}

→ Synthesis receives and preserves this structure → Report cites correctly
```

---

## Handling conflicting sources

When two credible sources contradict each other:

```
Source A (Stanford, 2025): "AI is reducing creative employment by 30%"
Source B (McKinsey, 2025): "AI is increasing creative productivity, not reducing employment"

❌ Wrong: Pick the more recently published source as authoritative
❌ Wrong: Average the claims ("AI has moderate impact on employment")
❌ Wrong: Pick whichever supports the report's narrative

✅ Correct: Document analyst reports both findings with annotations:
{
  "topic": "AI impact on creative employment",
  "findings": [
    {
      "claim": "30% employment reduction",
      "source": "Stanford AI Index 2025",
      "confidence": "high",
      "methodology": "longitudinal study of 500 studios"
    },
    {
      "claim": "Increased productivity, no employment reduction",
      "source": "McKinsey Global Institute 2025",
      "confidence": "high",
      "methodology": "survey of 1,200 creative professionals"
    }
  ],
  "conflict_detected": true,
  "conflict_note": "Conflicting findings from credible sources — methodology differences may explain divergence"
}

→ Coordinator or synthesis decides how to present the conflict; analyst doesn't resolve it
```

---

## Temporal data handling

When multiple sources were published at different times, temporal differences can be misinterpreted as contradictions:

```
❌ Source A (2020): "10% of studios use AI tools"
   Source B (2025): "60% of studios use AI tools"
   → Synthesised as: "Studies disagree on AI adoption rate" (wrong — it's temporal progression)

✅ With publication dates in structured output:
   Source A: {claim: "10% adoption", published: "2020-01-01"}
   Source B: {claim: "60% adoption", published: "2025-06-01"}
   → Synthesised as: "AI adoption grew from 10% (2020) to 60% (2025)"
```

**Rule**: Subagents must include `published_date` or `data_collection_date` in their structured output. Synthesis agents use this to interpret findings in temporal context.

---

## Report structure for multi-source findings

```markdown
## AI Impact on Creative Industries

### Well-established findings (multiple concordant sources)
- AI tools have been adopted by 60% of major studios (McKinsey 2025; AMPAS Report 2024; PwC Survey 2025)
- Post-production workflows have seen 35% efficiency gains (...)

### Contested findings (credible sources disagree)
- **Employment impact**: Stanford AI Index 2025 reports 30% job reduction in VFX roles;
  McKinsey Global Institute 2025 finds no significant employment change, citing productivity gains.
  Methodological differences (longitudinal vs cross-sectional) may explain the divergence.

### Data gaps
- Patent landscape not covered (patent database unavailable during research)
- Academic sources from 2024-2026 underrepresented (access limitations)
```

Well-established vs contested vs gaps — this structure is honest and citable.

---

## ⚠️ Exam traps

| Wrong answer | Correct answer |
|-------------|---------------|
| "Subagents return prose summaries; synthesis extracts citations later" | Require structured claim-source mappings from the start |
| "Pick the more recent source when sources conflict" | Document both with annotations; let coordinator/synthesis reconcile |
| "Combine conflicting statistics into an average" | Preserve both with source attribution and note the conflict |
| "Publication date isn't needed if the facts are current" | Always include dates — temporal differences are misread as contradictions without them |

---

## Related topics

- [Multi-Agent Coordinator](../01-Agentic-Architecture/02-multi-agent-coordinator.md) — coordinator decides how to handle conflicts in synthesis
- [Subagent Context Passing](../01-Agentic-Architecture/03-subagent-context-passing.md) — structured data format for inter-agent communication
- [RAG & Retrieval](../00-Foundations/02-rag-and-retrieval.md) — retrieval systems that surface source information
