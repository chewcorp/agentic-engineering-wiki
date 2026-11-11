---
type: concept
title: LLM-Driven Workflow Routing
description: >
  Escalating autonomy where an LLM chooses, generates, or prioritizes tasks
  instead of a fixed DAG — trading dependability for open-ended flexibility.
sources:
  - title: Prompt Engineering for LLMs
    resource: "Prompt Engineering for LLMs (Berryman), ch. 9"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 1, Deus Ex Machina: Orchestrated Autonomy (Table 1-2)"
---

Basic workflows may use LLMs inside tasks while routing between tasks stays a
fixed pipeline or graph ([AI pipeline orchestration](ai-pipeline-orchestration.md)).
LLM-driven routing moves the model into the *flow* itself. Escalating variants:

1. **Fixed task set, conversational orchestrator** — the workflow is an
   [LLM agent](../llm-agent.md) whose tools are the available tasks; on new work it
   chooses which task tool to invoke.
2. **Agent of agents** — tasks are themselves conversational agents with
   specialized tools; both levels need a finish act so they submit completed
   work rather than chat forever ([ReAct loop](react-loop.md)).
3. **On-the-fly task agents** — the orchestrator crafts a task agent (specialized
   system message and tool subset from a large inventory) as needed, rather than
   using only predefined per-task agents.
4. **Worklist management** — instead of one-at-a-time serial handoff, the
   orchestrator maintains a growing task list and continually prioritizes which
   work to pursue next.

Prefer a fixed graph first ([progressive agent architecture](progressive-agent-architecture.md)).
Each step toward LLM routing raises open-endedness and makes failures harder to
isolate than per-task debugging in a deterministic workflow. Role/delegation
frameworks and [stateful task agents](../harness/stateful-task-agents.md) are related
patterns for when goals demand that flexibility.

Each step up this ladder is a form of [agent control flow](agent-control-flow.md)
where the model itself, rather than the harness, decides branching — the same
tradeoff of flexibility against debuggability and evaluability that applies to
any [multi-agent architecture](multi-agent-architecture.md).

The book draws a useful boundary on this autonomy. Agents can route among
predefined paths, select tools or subagents, decide whether another step is needed,
revise their own prompts and toolsets within limits, and write small code to choose
the next step. They cannot redesign their own control graph, generate and validate
new topologies, create tools for themselves, or guarantee alignment when they change
strategy mid-run. Most production systems therefore sit between a fixed router and a
fully autonomous agent, with the choice made inside predefined states and
transitions. The finite-state view of that structure is in
[finite state machine control](finite-state-machine-control.md).
