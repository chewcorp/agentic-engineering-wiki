---
type: concept
title: Structured Memory Updates
description: >
  Applying memory changes through explicit rules for superseded facts, category replacement, duplicate suppression and confidence thresholds keeps a long-term profile from accumulating contradictions and stale entries.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 10, Structured Update Application (apply_updates); Deterministic Reconciliation; Semantic Compaction"
---

Memory updates work better as an explicit procedure than as a free-form rewrite. The
book's update step applies a set of structured changes to a store of facts, and each
rule is simple. An update can remove facts by listing them, or by naming the
identifiers they supersede, so a contradiction is removed on purpose rather than left
to the reader. A category can be set to replace older facts, while the default for
any other category is to accumulate. Duplicates are suppressed by comparing a
normalised form of the content. A confidence threshold rejects weak facts, with a
default of 0.7, and a missing confidence is treated as 0.5. A cap on the number of
facts, 100 by default, keeps the highest-confidence facts when the store is truncated.

Two further operations keep a profile usable over time. Deterministic reconciliation
removes older facts that conflict with a known pattern, such as dark mode against
light mode, choosing the older fact by its timestamp and removing it, without a
model call. Semantic compaction merges many small entries into one consolidated
record, drops stale contradictions and keeps the newest timestamp, then deletes the
originals. The book's advice is to compact when the store is noisy or too fragmented
to inject efficiently.

The identifiers need thought too. The book's examples generate short random hex
identifiers, which can collide in principle. This is a non-issue for one store and a
trade-off at very large scale. The rules above depend on the contents being correct
at write time, so they run after [validation](validate-before-durable-memory-write.md)
and after the cost-tiered decision to store. The general strategies for contradictions
are listed in [memory management](memory-management.md), and this note gives the
explicit rule set. Summarising a long history into fewer words is a different step,
covered in [memory summarization](memory-summarization.md).
