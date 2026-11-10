---
type: concept
title: Finite State Machine Control
description: >
  A finite state machine with explicit events, guards and actions gives an agent workflow a control grammar that stays recoverable when the agent alternates among a few stable modes.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 1, Finite and Hierarchical State Machines"
---

Treat an agent workflow as a finite state machine. The state is a compact
snapshot of what the system knows, such as the message list, routing hints and
progress markers. An event is something that happened since the last decision,
such as a tool being invoked or returning. A guard is a predicate that chooses
the transition from the current state and event. An action is the work done
during the transition: call a tool, append a message, save a checkpoint or ask
a human. Termination is the end condition, and start and end are distinguished
states.

The argument for this framing is that any agent needs the same loop: track
state, decide an action, observe the result, update context and stop. State
machines have been the standard grammar for that loop in compilers, GUIs,
embedded systems and network protocols, so the mapping is a blueprint rather
than a loose analogy. The FSM is a good fit when behaviour alternates among a
few stable modes and recoverability matters. Checkpointing at each state
boundary lets a process that crashes at step four of ten resume from step four.

Writing the states down also forces the handoff conditions into the open. A
blog-post workflow becomes ideation, outline, drafting, editing and search
optimisation, and the transition from outline to drafting must say which
outline output triggers drafting. Each state can then be tied to the tools it
needs, which counters the inconsistent results of one unstructured prompt.

The limit is growth. Adding planning, reflection, approval gates and retries
multiplies edges, and every edge encodes its own event, guard and action, so a
flat graph duplicates logic or turns into spaghetti routing. The fix is
grouping shared policy under a parent state, described in
[hierarchical state machine control](hierarchical-state-machine-control.md).
Keep the flat machine as the first version, in line with
[start simple and own the control flow](start-simple-own-control-flow.md), and
note that [agent control flow](agent-control-flow.md) describes the same choice
from the point of view of plan shape. The state the machine carries is
discussed in [structured run state](structured-run-state.md).
