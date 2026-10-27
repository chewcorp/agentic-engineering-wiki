# agentic-engineering-wiki

A curated wiki of agentic engineering concepts in one OKF bundle, `wiki/`.
Authoring rules are in [`docs/wiki-rules.md`](docs/wiki-rules.md). Read them
before you add or edit a note.

## Code Review Rules

### Review calibration

- Scope review to the maturity and risk of the changed surface. Apply full
  behavioural scrutiny to shipping code, schemas, scripts, and live
  workflows. For proposed or unwired material, check stage-appropriate
  completeness, internal consistency, claims about current behaviour, and
  runnable commands; do not require implementation explicitly deferred to a
  later decision.
- Trace a changed invariant through affected consumers before commenting. One
  shared root cause gets one structural finding listing the affected sites
  and canonical enforcement point. Keep unrelated causes separate.
- Make each finding independently checkable: state one defect, its exact
  evidence or verification path, its consequence, and the smallest safe
  remedy. On later rounds, retain prior dispositions, label causal follow-ons,
  and escalate repeated classes structurally instead of rediscovering sites.
  Declare convergence only when no prior blocking finding remains unresolved
  and a complete changed-surface pass finds no actionable finding, whether new
  or recurring.

### What a finding is in this repository

Notes under `wiki/` are curated summaries of the sources in their `sources:`
frontmatter. The owner writes or approves every input; review is not
hardening against adversarial content. A finding on a note must show at least
one of these:

- it misstates, overstates or drops a qualifier from its cited source;
- it contradicts another note;
- it breaks a rule in `docs/wiki-rules.md`.

Coverage belongs to curation, not review. That rules out three kinds of
finding:

- asking for newer or additional evidence;
- asking to widen a note's scope;
- asking for new rules, fields or tooling.

If a claim can't be checked because its cited source can't be read, raise no
finding on it, and don't substitute figures from memory.

### Author rules

- Fix an accepted cause at every site in the bundle, not only where it was
  flagged, and list the sites in the reply.
- Decline an out-of-scope finding in one line that names the rule above.
- After two review rounds with findings on one pull request, stop pushing
  and escalate the pattern to the owner. That is structural escalation, not
  convergence.

## Cursor Cloud specific instructions

The docs site is already running at `http://127.0.0.1:8000` (`mkdocs serve` from this repo). Use that process. A second `mkdocs serve` fails because port 8000 is taken.

`okf` and `mkdocs` are on `PATH`. Dependencies live in the gitignored `.venv`. From this repo:

```bash
okf validate --bundle agentic-engineering
okf graph --bundle agentic-engineering --broken
okf index --bundle agentic-engineering --recurse
mkdocs build --strict
okf search --bundle agentic-engineering "tool inventory size"
```
