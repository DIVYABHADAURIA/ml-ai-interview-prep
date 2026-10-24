# AI Agent Harness

## The one-line answer

**Agent = Model + Harness.** The model is the brain that reasons and picks the next step. The harness is the software around it (tools, memory, workspace, guardrails) that turns those decisions into real actions.

| Part | Role | Analogy |
| --- | --- | --- |
| Model | Reasons and generates output | The brain |
| Harness | Runs tools, manages memory, enforces rules | The body and workspace |
| Agent | The whole working system | A worker who thinks and acts |

**The ReAct loop** (Yao et al., 2022) is how the two interact:

1. **Reason**: the model reads its context and chooses an action.
2. **Act**: the harness executes it (tool call, sandboxed code, API, storage write).
3. **Observe**: the harness feeds the result back as new context.
4. **Repeat** until the task is done.

Example: a coding agent proposes a fix, the harness runs tests in a sandbox, and failures go back to the model to retry.

## Building blocks of a production harness

Each component fixes one limitation of a raw model.

| Component | What it does | Interview angle |
| --- | --- | --- |
| System prompt | Standing instructions: role, goal, rules | Bad prompts are a top cause of inconsistent behavior |
| Tools | Functions the model can call; the harness executes them | Trend: fewer narrow tools, more general code execution |
| Sandbox | Isolated space to run code or actions | Safe experimentation, easy reset, parallel agents at scale |
| Filesystem | Durable files that persist across sessions | Long tasks accumulate progress; humans and agents share work |
| Memory and context | Decides what stays in context vs. gets summarized | Context compaction; resume work across sessions |
| Feedback loops | Tests, checks, self-review after each action | Lets agents catch and fix their own errors |
| Guardrails / human-in-the-loop | Blocks unsafe actions; requires approvals | e.g. approval before deleting files, messaging customers, purchasing |
| Observability | Logs, traces, dashboards of what and why | Debugging, audit trails for regulated industries, feeds evaluation |

## Why the harness matters

As models converge in capability, the harness increasingly decides real-world performance. A strong harness on a mid-tier model can beat a weak harness on a stronger one.

- **Proof point:** GPT-5.5 with Databricks' OfficeQA Pro Agent Harness scored 52.63% vs. 36.10% for GPT-5.4, cutting errors nearly in half.
- **Evolution of the discipline:** prompt engineering (wording inputs) → context engineering (what the model sees, RAG, memory) → harness engineering (the whole system). The first two live inside the third.
- **Enterprise problem:** dozens of agents across teams cause *agent sprawl*. The fix is shared harness infrastructure, e.g. Agent Bricks, with governance via Unity Catalog and observability/evaluation via MLflow, across OpenAI, Anthropic, Google and open-source models.
- **Future:** models will absorb some planning and self-correction, but harnesses persist. Watch for *disposable harnesses* (one-off, per-task) and *natural-language agent harnesses (NLAHs)* configured in plain language.

**Common failure modes** (most production failures come from the harness, not the model): context rot · tool overload · brittle tool wiring · latency (10s+) · irrelevant retrieval · weak verification · missing guardrails.

## Case study: Databricks Genie (text-to-SQL)

Genie answers business questions by writing and running SQL. Most of its quality comes from the harness, not the model. Each harness layer maps to a building block above.

| Layer | How Genie does it | Building block |
| --- | --- | --- |
| Ground | A curated knowledge store (table/column descriptions, synonyms, business rules, instructions) plus trusted queries, i.e. SQL verified by a human that Genie reuses for matching questions | Memory and context |
| Guard | Read-only execution, so generated SQL can't modify data. Unity Catalog applies the asking user's own permissions, so Genie only sees what that user can see | Guardrails |
| Verify | Inspect reviews and corrects the generated SQL before showing a result. Failed queries are retried with the error fed back to the model | Feedback loops |
| Loop | Agent mode turns single-shot SQL into a multi-step ReAct loop: plan, run a query, observe results, write the next query, then synthesize an answer | ReAct loop |

**Why it's a good harness example:** text-to-SQL fails mostly on missing business context ("what counts as an active customer?"), not on SQL syntax. Curating that context and verifying outputs fixes more errors than switching models does.

## Likely interview questions

1. **What's the difference between an agent and a harness?** The agent is the full system; the harness is its execution layer (tools, memory, guardrails, workflow control). You talk to the agent; the harness makes it work.
2. **Harness vs. prompt engineering?** Prompting tunes the input; harness engineering designs the whole surrounding system. Prompting is one piece of it.
3. **Why can the harness matter more than the model?** It drives reliability through memory, orchestration, verification and guardrails. Upgrading only the model gives smaller gains if the scaffolding is unstable. Cite the 36.10% → 52.63% result.
4. **How would you fix context rot?** Context compaction: summarize or trim older turns, persist key state to files or memory, retrieve only what's relevant.
5. **How do you govern agents at scale?** Central control of data access, evaluation, audit, cost, and model choice through a shared platform layer instead of per-team harnesses.
6. **Walk through the harness of a text-to-SQL agent.** Use Genie: *ground* it with a knowledge store and trusted queries, *guard* it with read-only execution and per-user Unity Catalog permissions, *verify* with Inspect and retries on errors, and *loop* with Agent mode for multi-step questions.

## Closing soundbite and source

> The model holds the intelligence; the harness turns it into reliable work. Strong harnesses make average models useful, and weak ones waste the best models.

Related: Databricks' open-source meta-harness, Omnigent (June 2026), sits one layer above harnesses like Claude Code and Codex to compose, govern and share agents.

Source: [What is an AI Agent Harness? (Databricks Blog, June 2026)](https://www.databricks.com/blog/ai-harness)
