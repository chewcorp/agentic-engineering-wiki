---
type: concept
title: Deterministic Session Replay in CI
description: >
  Curated slices of recorded agent sessions become golden fixtures that CI
  replays offline, without models, tools or network, to check that a harness
  change has not silently altered the context the model is shown.
evidence: weak
sources:
  - title: "Deterministic replay in CI"
    resource: "Meta Model API cookbook, Building with Muse Code, undated (retrieved 3 Oct 2026) — https://dev.meta.ai/docs/cookbook/deterministic-replay"
  - title: "Audit and resume agent sessions"
    resource: "Meta Model API cookbook, Building with Muse Code, undated (retrieved 3 Oct 2026), byte-deterministic export — https://dev.meta.ai/docs/cookbook/audit-agent-sessions"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 9, Trace-to-Benchmark Pipeline; Regression Workflow"
---

Most harness changes never touch the model. They change how history is
trimmed, which tool results are kept, how a summary is inserted or how
approvals are recorded. Each of these alters what the model is shown on its
next call, yet ordinary evaluations catch the change only indirectly, through
a shift in outcomes, and only if the evaluation happens to exercise that path.
Live evaluations are also slow, costly and noisy because the model is
non-deterministic.

Meta's Muse Code cookbook describes a cheaper check aimed at that layer.
Because every session is an append-only event log, a recorded session can be
cut down to the few events a regression needs and stored as a golden fixture,
together with the model-context projection the harness should build from those
events. In continuous integration (CI), replay rebuilds the projection from
the committed records alone, offline, with no model, tool or network calls,
and diffs it against the expectation. A merge is blocked on any diff.

The recipe's practices:

- Author fixtures by hand rather than convert raw logs wholesale.
- Review the expected projection alongside the fixture.
- Version the fixture schema, adding new versions rather than editing old
  ones.
- When behaviour changes on purpose, update the expectation in the same
  commit.

Its one sharp caveat is that the inspect command exits zero even when a
fixture has drifted, so CI must read the diff count.

The evidence is a single vendor recipe, undated and without results, so the
note is marked weak. It complements rather than replaces outcome evaluation:
replay proves the harness assembles the same context, not that the model then
behaves well. It is the deterministic, cheap end of
[in-loop regression control](../optimization/in-loop-regression-control.md),
a fast layer beneath the slower
[regression evals](capability-vs-regression-evals.md), and it depends on a
durable [session log outside the context window](../knowledge/session-log-outside-context-window.md).

An agent's own incident or task logs can double as this regression suite.
Recorded sessions, trimmed to the events that matter and with sensitive log
contents redacted, let a change to prompts, compaction or tool wiring be
checked against real work before it ships. That matters most because
[harness assumptions go stale](../harness-assumptions-go-stale.md) and get
revised often.

The book uses a related replay for model choice, which needs a model call. Critical
production failures are promoted into replayable cases, stored as JSON lines with a
reference answer, prior scores and evaluator notes. A candidate model runs each case
at temperature zero with a structured output, the same evaluation logic scores it,
and the mean scores are compared before any production change. In the book's
comparison a smaller model scored higher on step completion and step efficiency,
while the larger model scored higher on answer correctness, which is a reason to
compare whole configurations rather than a single score. The pattern also covers
fallback models, prompt changes and routing changes. Unlike the CI replay above,
this version calls a model, so it is slower and its results are not deterministic.
