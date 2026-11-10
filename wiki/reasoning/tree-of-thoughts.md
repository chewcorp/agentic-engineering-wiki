---
type: concept
title: Tree of Thoughts
description: >
  Deliberate inference that searches a tree of coherent intermediate thoughts
  with LM generation, evaluation, lookahead, and backtracking.
sources:
  - title: "Tree of Thoughts: Deliberate Problem Solving with Large Language Models"
    resource: "Tree of Thoughts (Yao et al.), pp. 1–14"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 2, Tree of Thoughts as an Engineering Shortcut"
---

Tree of Thoughts (ToT) generalizes [chain of thought](chain-of-thought-prompting.md)
by treating coherent text units (“thoughts”) as nodes in a search tree rather
than sampling one left-to-right chain. Each state is the input plus thoughts so
far. The harness answers four design questions: how to decompose thoughts, how
to generate candidates, how to evaluate frontier states, and which search
algorithm to run.

Thought steps should be small enough that the LM can sample diverse
continuations, yet large enough to judge progress — a token is usually too
fine; a whole document usually too coarse. Generators either sample next
thoughts i.i.d. from a CoT-style prompt (rich spaces such as paragraphs) or
propose several candidates in one sequential prompt (constrained spaces such as
words or equation lines, to reduce duplicates). Evaluation uses an
[LM search heuristic](lm-search-heuristic.md): independent values or a vote
across states. Search is typically BFS keeping the top-\(b\) states when depth
is small, or DFS with prune-and-backtrack when deeper exploration is needed.

IO prompting, CoT, [self-consistency decoding](self-consistency-decoding.md),
and self-refinement are special cases of limited-depth or limited-breadth
trees. Unlike [branch-solve-merge](branch-solve-merge.md), which fans out full
independent trajectories then merges finals, ToT explores and prunes *inside*
a reasoning process. It needs no extra training — a pretrained LM plus prompts
suffice — but multiplies generation cost. Use it when early irreversible
choices dominate failure and
[world-model-augmented planning](../orchestration/world-model-augmented-planning.md) needs
explicit lookahead rather than a single
[plan-and-solve](plan-and-solve-prompting.md) monologue.

**Task patterns (Yao et al.):** Game of 24 uses equation-line thoughts + BFS
(\(b=5\)) and sure/maybe/impossible values — ToT \(b=5\) ~74% vs CoT-SC@100
~9% / best-of-100 CoT ~49%; ~60% of CoT samples already fail after the first
equation step (left-to-right fragility). Creative Writing uses a depth-2
vote over plans then passages; iterative refine can be a third generation
mode (new thoughts by refining old ones) and sometimes closes the gap.
Mini Crosswords need DFS with prune+backtrack over word fills — ablating
either collapses game success; false prunes happen when the LM misjudges rare
words. Prefer ToT when the harness can define a thought unit and a cheap
[LM search heuristic](lm-search-heuristic.md); otherwise pay for best-of-\(k\)
sampling first under [offline prompt evaluation](../evaluation/offline-prompt-evaluation.md).

Treat ToT as a “System 2” overlay on associative LM generation: classical
search (A*-like) with heuristics from LM self-assessment, modular enough to
swap BFS/DFS or pair with other self-eval loops ([Reflexion](../orchestration/reflexion.md),
Self-Refine) without locking thoughts to code-only PAL. Decide whether the
extra autonomy is worth the bill via
[deliberate search cost tradeoff](deliberate-search-cost-tradeoff.md) — readable
high-level thoughts also improve interpretability versus opaque token-level
values, but raise misuse risk as agents decide more autonomously.

A lighter, staged form is also used in practice. A smaller model proposes a few
candidate approaches, a stronger model evaluates and selects one, and an
execution stage carries it out. The evaluator gets the stronger model because
its routing choice is the high-impact one. The book suggests this can sometimes
replace a full multi-agent team at lower cost and time, without giving a
comparison figure. Because the stages are fixed in code, it differs from
[supervisor routing](../orchestration/supervisor-routing.md), where a model picks
the next agent at each step.
