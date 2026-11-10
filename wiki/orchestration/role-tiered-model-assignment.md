---
type: concept
title: Role-Tiered Model Assignment
description: >
  Assigning each agent role a model tier, with fast models for narrow workers and a deliberate model for the decisions that gate everything downstream, puts the expensive reasoning where a wrong answer costs most.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 4, Worked Example: A Multi-Agent Research Team (Table 4-5)"
---

The book's multi-agent research team assigns a model tier to each role. The
semantic-research, patent-research, notetaker and writer roles run a fast
decoder-only open-weight model with thinking disabled. The analyst runs a
reasoning model with a fixed thinking budget and is cross-checked by an
encoder-only model that scores relevance. The supervisor runs a deliberate
proprietary model that decides routing and when the outcome is final. All
temperatures are zero.

The stated design point is one deliberate analyst that reasons carefully over
the evidence, checked by a modern encoder, while everyone else stays fast and
cheap. The book calls this a self-auditing system: the encoder checks relevance
and potential hallucination in addition to the model supplying its sources.

The reasoning behind the split is that a decision which filters or routes
everything downstream justifies a stronger model, while a draft produced under
narrow instructions does not. The same logic appears in
[tree of thoughts](../reasoning/tree-of-thoughts.md), where the evaluator is
stronger than the proposer because its routing decision is the high-impact one.
Narrow prompts are what make the tiering safe. In the book the analyst must call
one tool exactly once and return only that result, so the supervisor sees a
bounded output whatever model produced it. The [model router](../model-router.md)
note makes a related choice per query rather than per role.

The cost is that every tier is a separate model to integrate, price and test.
Each tier needs its own [per-task offline harness tests](../evaluation/per-task-offline-harness-tests.md),
and the mix should be judged against the [agentic cost multiplier](agentic-cost-multiplier.md),
which counts calls per request rather than cost per call. The evidence here is
one worked example from one book, with no comparison against a single-model
team.
