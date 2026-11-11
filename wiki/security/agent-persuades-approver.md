---
type: concept
title: Agent Persuades the Approver
description: >
  A compromised agent can write a convincing justification for a harmful action, which turns a human approval gate into a bypass unless the approver is shown the concrete effect of the call rather than the agent's rationale.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 12, Social Engineering and Human-in-the-Loop"
---

The book's threat model points out that an agent can be the social engineer in
its own system. If the agent is compromised, for instance through indirect
prompt injection, it can use its reasoning to manipulate the person who approves
its actions. A request to delete a database is presented as an optimisation: the
agent has identified four hundred redundant legacy records causing latency, it
recommends a targeted purge, and asks the approver to click approve. The primary
safety mechanism, the human gate, has become a bypass.

The approver is only as informed as the request they see. If the approval prompt
carries the agent's own account of why the action is good, the approver is judging
the agent's argument rather than the action. The book's concern is that a plausible
justification from a system the approver trusts is persuasive by construction.

A design consequence, not stated in the source, is to show the approver the effect
the harness computes for the call: the target, the count, the arguments as
executed, and any dry-run result the tool produces, while the agent's rationale
is shown as context at most. Approval should bind to a fixed payload, so that the
action that runs is the one that was approved and the agent cannot change it after
approval. For irreversible actions, the review should also be something the agent
cannot write. These checks sit alongside the [human approval gates](../harness/human-approval-gates.md)
and [durable approval state](../harness/durable-approval-state.md), which keep an
approval in force across restarts. The reviewer should also see the exact
arguments, as in [editable tool-call correction](../harness/editable-tool-call-correction.md).
The source treats this as a scenario, with no measured incident rate. The entry
point for the attack is [indirect prompt injection](indirect-prompt-injection.md),
and the wider picture is in [layered agent threat model](layered-agent-threat-model.md).
