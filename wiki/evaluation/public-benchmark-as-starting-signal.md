---
type: concept
title: Public Benchmark as Starting Signal
description: >
  Public benchmarks are a reasonable way to shortlist models, but they cannot show how a model behaves inside a specific agent with its own prompts, tools and users, so evaluation must move to the system's own behaviour.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 8, Evaluation Lifecycle and False Confidence; ch. 9, Benchmarks Aren't Static Artifacts"
  - title: "BetterBench"
    resource: "Reuel et al., 2024, cited in AI Agents (Koenigstein), ch. 9"
---

The book describes three stages of assessment. Public benchmarking preselects
models on standardised datasets for general capabilities such as reasoning,
coding and tool use. Evaluation observes the real system: it traces the agent's
actions, and it analyses failures under realistic conditions. Custom benchmarking
turns what evaluation found into domain tasks, edge cases and failure scenarios,
which are then used to compare models, test fallbacks and check changes to
prompts, tools and architecture in a repeatable way.

Public scores are a starting signal, not proof of production reliability. The
book's reason is that both the model and the agent are probabilistic, so behaviour
varies across runs, contexts, tool interactions and users. A benchmark can say
how a model does on the benchmark's tasks. It cannot say how the model behaves in
an architecture with your prompts, your tools and your users. That is why the
book runs its own tests in parallel with the prototype, and why it treats them as
part of the development loop rather than a final step.

The comparison unit should match that point. Comparing a model alone on a public
benchmark leaves out the prompt, the tools and the strategy, so the honest unit is
a model, program and strategy combination, as in
[the comparison unit for LM pipelines](lm-pipeline-comparison-unit.md). Custom
benchmarks should be versioned, and the book's maintenance advice is to create a
new version when the system or its users change rather than edit the old one, since
new models expose new failure modes. The book cites BetterBench on how benchmarks
lose reliability over time. Practical sequencing follows from this: shortlist with
public scores, then build the custom set from production traces and stress
scenarios, as in [stress-dimension scenarios](stress-dimension-scenarios.md) and
[implicit user signals as failure proxies](implicit-user-signals-as-failure-proxies.md).
For a shared starting set, see [example suite](example-suite.md).
