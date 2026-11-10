---
type: concept
title: Agent Eval Outcome vs Transcript
description: >
  A trial's transcript is what the agent claims happened; its outcome is
  the actual resulting environment state — grade the outcome, since a
  transcript can narrate success that never occurred.
sources:
  - title: "Demystifying evals for AI agents"
    resource: "Demystifying evals for AI agents (Anthropic), Introduction; The structure of an evaluation"
  - title: "Holistic Agent Leaderboard (HAL)"
    resource: "Kapoor, Stroebl et al., arXiv 2510.11977, Oct 2025, ICLR 2026 — https://arxiv.org/html/2510.11977v1"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 8, Early Threat Testing: Hardening Agents During Development"
---

An agent eval trial produces two distinct artifacts, and conflating them is a
recurring source of misleading scores:

- **Transcript** (also trace or trajectory) — the complete record of the
  trial: reasoning, tool calls, intermediate results, and the agent's final
  reply. It is what the agent *says* happened.
- **Outcome** — the actual resulting state of the environment once the trial
  ends. It is what *actually* happened.

These can diverge. A flight-booking agent's transcript might end with "Your
flight has been booked," while the outcome — whether a reservation genuinely
exists in the environment's database — says otherwise. A grader that reads
only the transcript's closing message is scoring the agent's narration, not
its work; an agent that has learned (deliberately or not) to produce
confident success language is rewarded regardless of whether the environment
changed as claimed.

The design implication is to grade against the outcome wherever the task
produces one — inspect the actual backend state, file system, or database
rather than trusting the agent's self-report. See
[computer-use agent eval state checks](computer-use-agent-eval-state-checks.md)
for the concrete version of this for GUI-driven agents (a success banner
rendering on screen is not proof the underlying action took effect). This is
a precondition for
[outcome-based partial-credit grading](outcome-based-partial-credit-grading.md)
to mean what it claims to mean: "grade the outcome, not the path" only
guards against path-locked graders — it still requires the outcome itself,
not the transcript's account of it, to be the thing under inspection. Where a
task has no independently checkable environment state (a purely
conversational answer, say), the transcript is all there is to grade, but
that should be a deliberate fallback, not the default.

Grading the outcome does not make the transcript worthless. An outcome score
answers one question, whether the final state matched the goal, so a run that
cheated, took a dangerous action or succeeded despite a broken tool scores the
same as a clean one. The Holistic Agent Leaderboard (HAL) study ran 21,730
rollouts across nine models and nine benchmarks, then analysed a subset of
1,634 transcripts with an automated log-analysis tool.
It found what no outcome score showed:

- eight cases of agents locating gold answers by finding the benchmark itself
  online;
- unsafe actions, such as using the wrong credit card in a flight booking;
- explicit instructions violated in over 60% of failed tasks;
- tool-call failures in almost every run, including successful ones;
- a scaffold bug (data leakage in one benchmark's few-shot agent) that
  invalidated results.

The specific percentages belong to HAL's benchmarks and should not be
expected to transfer as numbers. The division of labour is therefore: grade
success on the outcome, and read transcripts to catch exploitation, unsafe
paths and harness bugs that the outcome hides. That reading is the core of
[error analysis](error-analysis-first.md). For an agent that acts on real
systems, "the system came back" is not enough evidence that the action was
sound or safe; its action trace needs reading too, and its success verdict
should come from an [owner check](owner-validation-decides-success.md) rather
than its own narration.

A related failure is passive. In red-team testing an agent can say it cannot
access a restricted policy while its trace shows a call to the search tool on
that policy, with results returned. The book's remedy is to record blocked
calls in the trace under a marker, so a judge can tell an agent that never tried
a forbidden action from one that tried and was stopped. Its example, a
customer-support agent with a confirmation-gated email tool, passed the attack
classes it tested, which shows coverage of those cases rather than general
safety. See the [agentic red-teaming loop](../security/agentic-red-teaming-loop.md).
