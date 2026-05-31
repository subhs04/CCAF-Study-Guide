# 01 · Agentic Architecture & Orchestration — 27%

**Highest-weighted domain.** Covers how Claude-powered agents execute tasks autonomously, delegate to subagents, handle errors, enforce workflows, and manage state.

Scenarios tested: Multi-Agent Research System · Customer Support Resolution Agent · Developer Productivity

| File | Topic |
|------|-------|
| [01-agentic-loop.md](./01-agentic-loop.md) | The core execution loop: stop_reason, tool calls, loop control |
| [02-multi-agent-coordinator.md](./02-multi-agent-coordinator.md) | Hub-and-spoke architecture, partitioning, isolation |
| [03-subagent-context-passing.md](./03-subagent-context-passing.md) | Task tool, allowedTools, explicit context passing, parallel spawning |
| [04-multi-step-workflows.md](./04-multi-step-workflows.md) | Enforcement vs prompt guidance, prerequisite gates, handoff protocols |
| [05-agent-sdk-hooks.md](./05-agent-sdk-hooks.md) | PostToolUse, PreToolCall hooks — deterministic enforcement |
| [06-task-decomposition.md](./06-task-decomposition.md) | Sequential (prompt chaining) vs dynamic adaptive decomposition |
| [07-session-state-and-forking.md](./07-session-state-and-forking.md) | Session resumption, fork_session, scratchpad persistence |

← [Back to root](../README.md)
