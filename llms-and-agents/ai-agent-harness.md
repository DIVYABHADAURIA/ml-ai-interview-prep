# AI Agent Harness

## Core idea

**Agent = Model + Harness.** The model (brain) reasons and picks the next step; the harness (body) runs tools, holds memory and enforces rules.

**ReAct loop:** Reason (model picks an action) → Act (harness executes it) → Observe (result fed back) → Repeat until done.

## 8 building blocks

| Component | One-liner |
| --- | --- |
| System prompt | Standing role, goal and rules |
| Tools | Callable functions; trend is toward general code execution |
| Sandbox | Isolated, resettable place to run code |
| Filesystem | Durable files across sessions |
| Memory / context | Compaction: summarize old turns, resume later |
| Feedback loops | Tests and self-checks after each action |
| Guardrails | Block unsafe actions; human approval |
| Observability | Logs and traces for debugging, audit, evals |

## Why it matters

- **Harness can beat model:** GPT-5.5 + OfficeQA Pro harness hit 52.63% vs. 36.10% for GPT-5.4, nearly halving errors.
- **Evolution:** prompt → context → harness engineering; the first two live inside the third.
- **Enterprise:** shared harness infrastructure prevents agent sprawl (Agent Bricks + Unity Catalog + MLflow).
- **Future:** disposable harnesses and natural-language agent harnesses (NLAHs).
- **Failure modes:** context rot, tool overload, brittle tool wiring, latency, bad retrieval, weak verification, missing guardrails.

## Quick Q&A

- **Agent vs. harness?** Agent is the whole system; harness is its execution layer.
- **Harness vs. prompt engineering?** Prompting tunes inputs; harness engineering designs the whole system.
- **Fix context rot?** Compact old context, persist state to files, retrieve only what's relevant.
- **Govern at scale?** Central control of access, evals, audit, cost and model choice.

**Soundbite:** strong harnesses make average models useful; weak ones waste the best models.

Related: Databricks Omnigent (June 2026) is an open-source *meta-harness* that sits above harnesses like Claude Code and Codex to compose, govern and share agents.

Source: [What is an AI Agent Harness? (Databricks Blog)](https://www.databricks.com/blog/ai-harness)
