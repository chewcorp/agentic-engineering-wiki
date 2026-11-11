---
type: concept
title: Contain at the Environment Layer First
description: >
  Contain agents with deterministic environment boundaries (filesystem, egress,
  OS sandbox) first and steer at the model layer second, matching isolation
  strength to how well the overseer can evaluate the agent's actions.
evidence: moderate
sources:
  - title: "How we contain Claude across products"
    resource: "Anthropic Engineering, 25 May 2026 — https://www.anthropic.com/engineering/how-we-contain-claude"
  - title: "Beyond permission prompts: making Claude Code more secure and autonomous"
    resource: "Anthropic Engineering, 20 Oct 2025 — https://www.anthropic.com/engineering/claude-code-sandboxing"
  - title: "What I learned building an opinionated and minimal coding agent"
    resource: "Mario Zechner, 30 Nov 2025 — https://mariozechner.at/posts/2025-11-30-pi-coding-agent/"
  - title: "Designing agentic loops"
    resource: "Simon Willison, 30 Sep 2025 — https://simonwillison.net/2025/Sep/30/designing-agentic-loops/"
  - title: "Contained execution"
    resource: "Meta Model API cookbook, Building with Muse Code, undated (retrieved 3 Oct 2026) — https://dev.meta.ai/docs/cookbook/contained-execution"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, Sandboxing Agent Execution (three cumulative layers)"
---

An agent that can run code can do almost anything the account it runs as can
do, and the model's own judgement about what is safe can be subverted by
injected text or simply be wrong. The question is where to draw the line that
actually holds. Anthropic's 2026 containment post answers it with an ordering:
contain at the environment layer first, then steer at the model layer.
Deterministic boundaries, such as which directories are writable, which network
hosts are reachable and which operating-system (OS) sandbox wraps the process,
do not depend on the model behaving well. This sharpens the isolation item in
[agent system-level defenses](agent-system-level-defenses.md) into a priority
rule.

The same post adds a matching rule. Isolation strength should track the
overseer's capacity to evaluate what the agent does. Anthropic uses three
patterns: an ephemeral gVisor container for claude.ai, a human-in-the-loop
sandbox for Claude Code and a local virtual machine (VM) for Cowork. It also
advises wariness of custom components, noting that in its incidents the
standard primitives held while its own proxy failed. The earlier "Beyond
permission prompts" post describes the open-source OS-level sandbox runtime
behind this (Seatbelt on macOS, bubblewrap on Linux) with directory and
network-host allowlists, and the containment post reports 84% fewer permission
prompts once it was in place. Meta's Muse Code cookbook adds a step neither
post describes: before trusting its OS sandbox, the harness tries a write the
policy forbids, and if that write lands it reports the sandbox unavailable and
refuses to run any command. Its recipe also concedes that network-dependent
tasks may need the sandbox disabled, and it uses the same read-only mounts to
protect the agent's own rules (see
[immutable agent guardrails](immutable-agent-guardrails.md)).

Practitioners agree on containers but disagree on prompts. Zechner runs his Pi
agent in YOLO mode, arguing that once an agent can execute code, permission
prompts are theatre and container isolation is the real boundary. Willison
likewise recommends Docker or Codespaces sandboxes with test-only,
budget-capped credentials. Against Zechner, Willison and Meta's
[Rule of Two](agents-rule-of-two.md) hold that containment alone is not enough
and capability combinations must also be limited. The point remains contested,
though both positions agree that the environment boundary comes first.

The book's account of code execution gives three layers that stack. Runtime
isolation limits where code runs, using containers or virtual machines at the
operating-system or hardware boundary. Ephemeral environments limit how long state
lives, since each is created for one task or run and destroyed afterwards, which
stops state leaking between runs. Governed execution services limit who may run
what and when, by putting execution behind an interface where policy, budgets,
timeouts and approvals are enforced outside the agent. Each layer reduces the blast
radius of the one above it. The book also says a container is a packaging mechanism
and not a hard security boundary, which is why the layers are stacked. The
interpreter-level option is in [capability-scoped interpreter](capability-scoped-interpreter.md).

For any agent, then, its container, mounts and network reach are the primary
safety control, ahead of anything in its prompt. The worst mount, a container
daemon socket, is the first thing to scrutinise
([the Docker socket is root](docker-socket-is-root.md)), and it pairs with
keeping secrets out of the container
([credentials stay outside the sandbox](credentials-outside-the-sandbox.md)).
Model-layer and approval controls then stack on top, as described in
[layered agent permission stack](layered-agent-permission-stack.md) and
[hardline command floor](hardline-command-floor.md).
