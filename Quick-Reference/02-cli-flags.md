# CLI Flags Reference

← [Quick Reference index](./README.md)

---

## Claude Code CLI flags

| Flag | Short | Purpose | Critical for |
|------|-------|---------|-------------|
| `--print` | `-p` | Non-interactive mode — no stdin prompts | CI/CD pipelines (prevents hangs) |
| `--output-format json` | | JSON-formatted output | Machine-parseable CI findings |
| `--json-schema <path>` | | Enforce specific JSON schema on output | Structured extraction in CI |
| `--resume <session-name>` | | Resume a named prior session | Multi-session investigations |
| `--session-name <name>` | | Name the current session for later resumption | Long-running investigations |
| `--continue` | | Continue most recent session | Quick session resumption |
| `--model <model-string>` | | Override model for this invocation | Testing different tiers |

---

## CI/CD usage pattern

```bash
# Minimal: non-interactive review
claude --print "Review PR for security issues in: $(git diff --name-only HEAD~1)"

# With structured output
claude --print \
       --output-format json \
       --json-schema ./schemas/review-findings.json \
       "Review the following files for blocking issues only: ..."

# With session resumption
claude --resume "security-audit-payments" \
       "Continue from where we left off. New files changed: payments/refund.py"
```

---

## What happens without --print

```bash
# Without --print in a CI script:
claude "Review this PR..."
# → Waits for stdin
# → CI job hangs indefinitely
# → Pipeline times out after N minutes
# → Developer sees "Pipeline timed out" with no useful information
```

---

## /compact command (in-session)

Not a CLI flag — a slash command used during an interactive session:

```
/compact
```

Summarises and compresses earlier context to free up context window space. Use when context fills during extended exploration sessions.

---

## Model strings (as of May 2026)

| Tier | Model string |
|------|-------------|
| Haiku | `claude-haiku-4-5-20251001` |
| Sonnet | `claude-sonnet-4-6` |
| Opus | `claude-opus-4-6` |

← [Quick Reference index](./README.md)
