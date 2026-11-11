---
type: concept
title: Canonical Path Containment
description: >
  Resolving every path an agent supplies to its canonical form and checking that it stays inside one writable root defeats traversal, symlink and absolute-path escapes, provided every file operation goes through that single check.
evidence: moderate
sources:
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, Path Handling as Explicit Contract (Example 6-6, Table 6-2)"
  - title: "AI Agents"
    resource: "AI Agents (Koenigstein), ch. 6, Deploying in a Container (Example 6-5)"
---

An agent that can read or write files will sooner or later be handed a path that
leaves the directory it was meant to use. The book's rule is to make the path an
explicit contract. There is one writable root, set through configuration. Every
path the agent supplies is first canonicalised, so that relative segments and
symbolic links are followed to the real location. The canonical path must then be
relative to the root, and the check raises an error at once if it is not. Every
write goes through one function that performs this check, so no code path can skip
it.

The check blocks the escapes the book names: directory traversal such as a path
with repeated parent segments that climbs to a system file, a symbolic link inside
the root that points outside it, an absolute path to a sensitive file, and any
other route that ends outside the root. The book's principle is that every
filesystem interaction is untrusted until proven otherwise. Its observation from
practice is that an agent given file access did try to leave its working directory,
and the check turned the attempt into a clean failure rather than a write.

The container the book deploys reduces the blast radius but does not stop this
class of problem by itself. A non-root user and a fixed set of writable
directories limit what a process can change, but an agent can still write to a
writable directory it was not meant to use, which is why the check sits in the
application. Two points belong on the checklist. The check must run on the resolved
path, since a string comparison on the raw input can be defeated by encoding and
links. And a check followed later by a write can be raced if the path changes in
between. The second is a general concern rather than one the book raises.

This is the filesystem form of the rule in [contain at the environment layer first](contain-environment-first.md):
the boundary should be deterministic and independent of the model's judgement. The
same idea applies to command-line flags in [safe search argument passing](safe-search-argument-passing.md),
and to the isolation layers in [capability-scoped interpreter](capability-scoped-interpreter.md).
