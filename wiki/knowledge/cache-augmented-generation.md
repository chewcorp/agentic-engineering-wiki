---
type: concept
title: Cache-Augmented Generation
description: >
  Cache-augmented generation loads a small, stable body of knowledge into the context before answering, which is fast and suits rarely changing runbooks, but it is limited by cache size and goes stale when not refreshed.
evidence: weak
sources:
  - title: "Observability in the AI-Native Era"
    resource: "Lipsig, Grabner and Rati, ch. 2, RAG versus CAG and how they relate to LLMs"
---

The source treats retrieval-augmented generation and cache-augmented generation as
two ways to supply an LLM with data beyond its training. Retrieval-augmented
generation searches external sources when the question arrives, so the answer
reflects the data as it is at that moment. It suits broad or fast-changing datasets.
The source's observability example is a question about current system health, which
triggers live metric and log checks. That takes longer than an instant answer but is
accurate. See [retrieval-augmented generation](retrieval-augmented-generation.md) for
the general pattern.

Cache-augmented generation loads the relevant context up front and then answers from
it. Responses are faster because nothing is fetched at question time. It suits tightly
scoped datasets that change infrequently, such as runbooks. Its limits are that the
cache is bounded by its size, and that it goes stale. The source's example is a
system crash between retrieving logs and starting a session, which leaves the cache
holding data that is no longer current.

Both differ from fine-tuning. Fine-tuning changes the model's own knowledge and is the
choice for extending the model into a domain, especially with proprietary data. The
two augmentation methods keep the added data separate from the model, which lets it
change without retraining. The source notes that data can be indexed and weighted
under any of the three approaches to improve accuracy, which it presents as a
protection against errors in operations work.

Cache-augmented generation is about placing knowledge in context, so it is a
decision about the [static part of the prompt](../context/static-vs-dynamic-prompt-content.md).
It is not the same as [response caching](../response-caching.md), which reuses
outputs. For the choice between the methods and fine-tuning, see
[fine-tuning approach comparison](../fine-tuning-approach-comparison.md).
