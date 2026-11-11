---
type: concept
title: Memory Namespace Scoping
description: >
  Scoping every memory record by organisation, user and kind, with the user identifier taken from a verified session rather than a client string, keeps one user's memories out of another's reach and lets app and agent code share one service.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 10, Namespaces; Service Layer; Integrated Example"
---

Once memory extends beyond a single thread, it becomes an application concern. The
book scopes each record with a namespace tuple made of the organisation, the user
and the kind of memory, where kinds include semantic, episodic, procedural and
profile memory. Organisation-wide memory uses a shared segment in the same tuple,
so it is kept apart from any one user's records. Each kind exposes list, write and
delete operations, and the book's integrated example shows an administrator writing
an organisation-wide procedural policy that every user's agent then reads.

The identifier that fixes the namespace is the critical field. The book's security
note is that the user identifier must come from a verified token or an authenticated
session. A value the client sends can be changed, and then memory becomes writable
by anyone who can spoof it. Every read and write must be scoped from that verified
value, and the agent should not be trusted to choose the namespace.

The book also recommends a thin service layer. One interface offers the semantic,
episodic and procedural operations, and the application's API and the agent's nodes
call the same helpers. That gives one implementation rather than two that drift
apart. The same layer is where duplicate writes are skipped, because repeated
preferences would otherwise pile up across sessions. Encryption at rest for the
checkpoints that hold this state is the other half of the design; see
[checkpoint rewind does not rewind memory](checkpoint-rewind-does-not-rewind-memory.md)
for the store's history.

Scoping limits who can read a memory, not what it says. A poisoned record that is
correctly scoped still returns to the user who wrote it, so writes need checks of
their own, covered by [validate before durable memory write](validate-before-durable-memory-write.md)
and the threat in [memory hacking](../security/memory-hacking.md). The memory kinds
themselves are described in [agent memory tiers](agent-memory-tiers.md).
