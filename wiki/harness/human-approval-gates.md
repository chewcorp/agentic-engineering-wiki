---
type: concept
title: Human Approval Gates
description: >
  Explicit per-action automation levels so humans can supply plans, validate
  them, or approve irreversible tool calls before the agent proceeds.
sources:
  - title: "AI Engineering: Building Applications With Foundation Models"
    resource: "AI Engineering (Chip Huyen), ch. 6"
  - title: Prompt Engineering for LLMs
    resource: "Prompt Engineering for LLMs (Berryman), ch. 8"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 2, Surprised You're Joking, AI Agent: Designing Human-in-the-Loop Workflows"
---

Humans need not be fully absent from an [LLM agent](../llm-agent.md). They can
provide a high-level plan the agent expands
([hierarchical planning](../orchestration/hierarchical-planning.md)), validate a generated plan,
or execute risky steps themselves. The harness must define the automation level
allowed per action — for example, require explicit approval before a database
update, code merge, or bank transfer.

This is the agent-specific form of blast-radius control for
[write actions](agent-tool-categories.md): unreliable models should not hold
production delete keys by default. Pair gates with logging of
[function calling](function-calling.md) parameters so reviewers see what would
run before they approve — the same detail a
[tool-call transparency UI](tool-call-transparency-ui.md) surfaces to end
users. In product UIs, expose the same control as authorization prompts plus
inspectable/editable tool calls under
[agent UX steering affordances](agent-ux-steering-affordances.md).

Enforce the gate at the application layer, not the prompt layer: any tool call
with even a remote chance of being dangerous (modifying real-world assets)
must require explicit user authorization before execution via harness-level
interception, not by instructing the model to ask first — application
interception, not prompt-level pleading. Prompted requests for permission can
be skipped by the model under the same failure modes as any other
instruction; an approval step the harness enforces before dispatch cannot be.
Once given, an approval is state that must survive crashes and restarts
([durable approval state](durable-approval-state.md)).

Per-action gates sit inside two broader decisions for agents that act on live
systems. One is the default posture when nothing has been approved, which
should be read-only ([diagnose by default, act by
exception](diagnose-by-default-act-by-exception.md)). The other is which
situations the agent may handle alone at all: only those matching a prepared
runbook, with anything outside that envelope escalated as a planned step
([autonomy bounded by the prepared envelope](envelope-bounded-autonomy.md)).
Below the gates, some actions should be unavailable whatever is approved
([hardline command floor](../security/hardline-command-floor.md)).

In a LangGraph-style workflow an interruption takes one of four forms. The
approver can approve or reject before a critical step, with rejection routed to
another action. They can review and edit the state directly. They can inspect a
tool call's arguments before it reaches an external system. Or they can supply an
input the workflow needs to collect. Two cautions come with these. API calls and
database writes must not share a node with the interrupt, so that resuming does
not repeat them, which is the sequencing in [the effect sandwich](../orchestration/effect-sandwich.md).
And a revise path that loops back to the gate keeps the workflow predictable.
An approver shown only the agent's rationale can be persuaded, as
[agent persuades the approver](../security/agent-persuades-approver.md) describes.

Human involvement need not be limited to pre-execution approval. Queue human
reviewers for [workflow tasks](../orchestration/task-io-schema-design.md) whose output needs
human-level judgment the harness cannot yet automate. And when a
[Reflexion](../orchestration/reflexion.md)-based task keeps failing on the same narrow subset
of work items despite retries, that is itself a signal worth routing to a
human: have someone inspect the recurring failure case and adjust the prompt,
rather than letting the task keep burning retries against a problem retries
can't fix.
