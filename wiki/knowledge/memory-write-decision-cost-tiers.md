---
type: concept
title: Memory Write Decision Cost Tiers
description: >
  Judging every candidate memory with an LLM adds a hidden call per step, so a tiered mix of rules, embeddings, small classifiers and an LLM judge for hard cases keeps most of the value at a fraction of the cost.
evidence: weak
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 10, The Memory Storage Decision as a Parallel Tax (Table 10-5)"
---

Each agent step can produce candidates for memory, and deciding whether to store
one is a call that has a cost. If an LLM makes that decision on every step, it adds
a call per step, which the book calls a parallel tax. In a five-step ReAct loop that
is five extra calls, so the bill roughly doubles without appearing in the reasoning
trace.

The book's comparison, with illustrative precision figures, sets four options side
by side. Regex or rules add no calls, cost nothing, are predictable, and are brittle
for edge cases, with a figure of about 70% precision. Embedding similarity adds no
LLM call but needs embedding inference, at about 85%. A small encoder-only classifier
costs little to run and is put at about 90%. A full LLM judge adds one call per step
and is put at about 95%. The precision figures are the book's illustration, not
measurements, so the ranking is the part worth keeping.

The recommended approach is a tiered one. Use regex for structured data, embedding
similarity for retrieval relevance, encoder classifiers for lightweight memory
decisions, and an LLM judge only for the ambiguous, high-stakes cases. The book adds
a free step that needs no model: a deterministic pass that finds known conflicting
pairs, such as dark mode against light mode, and keeps the newer fact by timestamp.

The tiers apply the cost thinking in [agentic cost multiplier](../orchestration/agentic-cost-multiplier.md)
to memory specifically. The first tier is the same check as
[validate before durable memory write](validate-before-durable-memory-write.md), and
embedding-based linking appears in [embedding-plus-LLM memory linking](embedding-plus-llm-memory-linking.md),
which uses the same cheap-filter-then-judge pattern for connections between notes.
