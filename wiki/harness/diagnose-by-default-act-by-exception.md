---
type: concept
title: Diagnose by Default, Act by Exception
description: >
  An agent that operates live systems should run read-only by default and reach
  write actions only through review, a narrow deterministic action or a
  separately configured, time-bound elevated tier.
evidence: moderate
sources:
  - title: "Agent permissions"
    resource: "Microsoft Learn, Azure SRE Agent (modes, On-Behalf-Of elevation) — https://learn.microsoft.com/en-us/azure/sre-agent/permissions"
  - title: "Execute mitigations"
    resource: "Azure SRE Agent docs — https://sre.azure.com/docs/capabilities/execute-mitigations"
  - title: "HolmesGPT"
    resource: "CNCF Sandbox project, GitHub README — https://github.com/HolmesGPT/holmesgpt"
  - title: "Bits AI SRE deeper reasoning"
    resource: "Datadog blog, 5 March 2026 — https://www.datadoghq.com/blog/bits-ai-sre-deeper-reasoning/"
  - title: "AWS DevOps Agent: the diagnose-not-act line"
    resource: "Dora Noda, bex.co, 6 August 2026 (third-party commentary) — https://bex.co/blog/2026/08/06/aws-devops-agent-diagnose-not-act-line"
  - title: "openclaw-doctor self-healing proposal"
    resource: "OpenClaw GitHub issue #36455 — https://github.com/openclaw/openclaw/issues/36455"
  - title: "OpenClaw docs: Health check contract"
    resource: "OpenClaw official docs as of 2026.9.x, retrieved 3 Oct 2026 — https://docs.openclaw.ai/cli/doctor/health-contract"
  - title: "OpenClaw docs: Run doctor"
    resource: "OpenClaw official docs, retrieved 3 Oct 2026 — https://docs.openclaw.ai/cli/doctor/running"
  - title: "Hermes Agent docs: CLI reference"
    resource: "Nous Research official docs as of v0.21.5 (v2026.9.24), retrieved 3 Oct 2026 — https://hermes-agent.nousresearch.com/docs/reference/cli-commands"
  - title: "Using Headless CLI"
    resource: "Cursor docs — https://cursor.com/docs/cli/headless"
  - title: "Automations"
    resource: "Cursor docs, cloud agents — https://cursor.com/docs/cloud-agent/automations"
  - title: "Run Claude Code programmatically"
    resource: "Claude Code docs — https://code.claude.com/docs/en/headless"
  - title: "Non-interactive mode"
    resource: "OpenAI Codex docs — https://learn.chatgpt.com/docs/non-interactive-mode"
  - title: "Observability in the AI-Native Era"
    resource: "Lipsig, Grabner and Rati, ch. 6, Tip: Don't Mix Read and Write"
---

An agent with write access to a live system can make an incident worse faster
than any human. A wrong diagnosis followed by an automatic restart, config
rewrite or rollback compounds the original fault, and LLM diagnosis is often
wrong (see [LLM SRE agent success varies by benchmark and fault class](../evaluation/llm-sre-agents-low-success.md)).
[Human approval gates](human-approval-gates.md) set the automation level per
action; the prior question is what posture the agent takes when nothing has
been approved.

**Products converge on read-only.** The 2026 AI site reliability engineering
(SRE) products give the same answer. HolmesGPT is read-only by design and
respects role-based access control, with limited write actions available only
through a separate remediation integration. Azure SRE Agent offers ReadOnly,
Review (each action approved) and Autonomous modes, can run under a Reader
identity that requests temporary elevation, and always blocks `delete`,
`remove` and Key Vault commands. Datadog's Bits AI SRE runs hypothesis-driven
investigations and offers human-in-the-loop triage actions. For AWS DevOps
Agent, a third-party commentary argues that it deliberately diagnoses but does
not act, producing mitigation plans that humans execute; that framing is
opinion. A community proposal for a self-healing agent on the OpenClaw
platform, a prototype of about 500 lines, states the same rules for an agent
repairing agent infrastructure: diagnose before acting, never blind-restart,
block dangerous commands, require approval for config changes and cap
iterations.

**Diagnostic tools split detect from repair.** The same separation shows up one
level down, in the diagnostic commands an agent calls. OpenClaw's `doctor` is
built on a health-check contract in which each check implements a read-only
`detect()` returning structured findings (check ID, severity, message, fix
hint) and an optional `repair()` that runs only under `--fix`; after a repair,
detection re-runs and warns if the finding persists, so a fix is verified by
the check that raised it. Hermes's `hermes doctor` is read-only without `--fix`
and exits non-zero while problems remain, which makes it usable as a gate. The
trap is that the obvious unattended invocation is not always the read-only one:
OpenClaw's docs (as of 2026.9.x) say plain `doctor`, including
`doctor --non-interactive`, can migrate state without `--fix`, because the flag
suppresses prompts, not writes. An agent should diagnose only through postures
documented as read-only (there, `--json` or `--lint`), and treat a write
posture as an exceptional, approved action. Structured findings and exit codes
are also the cheap score that decides whether to wake an LLM at all (see
[a deterministic score gates LLM diagnosis](../orchestration/deterministic-score-gates-llm-diagnosis.md)).

**Headless coding agents make a natural diagnose tier, but only with enforced
confinement.** The coding-agent command-line interfaces ship different
defaults, and those defaults change between versions. Don't treat a missing
"force" or "yes" flag as read-only. Cursor's non-interactive print mode, for
example, has been documented both as proposing changes unless `--force` is
passed and as having full write and shell access. A diagnose tier should
select an explicit read-only or ask-only mode where the CLI has one, restrict
tools by allow-list, and run inside a sandbox whose filesystem and network
permissions enforce the read-only boundary whatever the CLI does. Check each
CLI's current documentation for the version you deploy. Codex `exec` defaults to a read-only sandbox, with
`workspace-write` and `danger-full-access` as explicit escalations. Claude Code
`-p` supports `--allowedTools` rules and a `dontAsk` mode that denies anything
that would prompt. Two loading behaviours matter. Without `--bare`, Claude
Code's `-p` runs a repository's hooks and `.mcp.json` servers with no trust
dialog, and Cursor Automations warn that memories persist across runs and
advise connecting only trusted Model Context Protocol servers. Building two
tiers from these defaults is an inference, not a documented product pattern: a
diagnose tier that runs each CLI in an explicitly selected read-only mode,
with a narrow tool allow-list, sandbox-enforced read-only permissions and
machine-readable output, and a separate act tier
that runs bare (no repository hooks or MCP servers), under a distinct identity,
with elevation granted per action for a limited time, as Azure SRE Agent's
On-Behalf-Of elevation does. Neither tier should hold long-lived credentials
(see [credentials stay outside the sandbox](../security/credentials-outside-the-sandbox.md)),
and both sit inside the
[layered agent permission stack](../security/layered-agent-permission-stack.md).
Splitting tiers is also how such an agent drops a leg of
[the agents Rule of Two](../security/agents-rule-of-two.md): the tier that reads
[untrusted logs](../security/logs-are-untrusted-input.md) holds no write power.

The same split can be made at the level of tool servers. One observability guide
separates data-access tools, such as log and trace queries, from configuration and
triggering tools, and installs them as separate MCP servers, so a data-only server
cannot change settings or start automation. Its reasons are accidental changes and
a smaller surface for prompt injection. It also observes that users tend to enable
every tool by default, which defeats the split unless the default changes. Which
tools exist at all is the subject of [question-driven tool design](question-driven-tool-design.md).

When a write is warranted, it should be reached through review or a narrow,
deterministic action inside the
[prepared envelope](envelope-bounded-autonomy.md), applied by code from the
model's proposal ([the LLM proposes, code applies](llm-proposes-code-applies.md))
through a [narrow action verb](narrow-action-verb-set.md). In a durable runtime
only the read-only diagnostics should be
[marked replay-safe](replay-safe-tool-annotation.md), and any approval obtained
along the way should be [durable](durable-approval-state.md).
