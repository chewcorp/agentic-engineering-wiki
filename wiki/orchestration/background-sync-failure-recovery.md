---
type: concept
title: Fail-Loud Background Sync and Safe Divergence Recovery
description: >-
  Unattended Git sync jobs should expose failures by class and recover from divergence only through verified, non-destructive integration.
evidence: weak
sources:
  - title: "Dealing with non-fast-forward errors"
    resource: "GitHub Docs — https://docs.github.com/en/get-started/using-git/dealing-with-non-fast-forward-errors"
  - title: "git-push"
    resource: "Git documentation — https://git-scm.com/docs/git-push"
  - title: "git-pull"
    resource: "Git documentation — https://git-scm.com/docs/git-pull"
  - title: "git-rebase"
    resource: "Git documentation — https://git-scm.com/docs/git-rebase"
  - title: "git-sync v4 README"
    resource: "Kubernetes git-sync — https://github.com/kubernetes/git-sync/blob/master/README.md"
  - title: "Git Repositories"
    resource: "Flux documentation — https://fluxcd.io/flux/components/source/gitrepositories/"
  - title: "Error: Permission denied (publickey)"
    resource: "GitHub Docs — https://docs.github.com/en/authentication/troubleshooting-ssh/error-permission-denied-publickey"
---

An unattended synchronizer needs an observable success condition, not merely evidence that its process ran.[unverified] Git-sync provides an optional error file and configurable consecutive-failure limits, while Flux exposes readiness and a resolved revision; both illustrate explicit status reporting in pull-side systems.[5][6] Those products do not prove a specific policy for a multi-writer Git publisher.

Keep failure classes distinct where the available evidence permits, and preserve the original command output and phase rather than inferring a cause from a generic nonzero exit.[unverified] GitHub documents non-fast-forward rejection as protection against losing history and recommends fetching and integrating before retrying.[1] Git's `pull --ff-only` fails when local and remote branches have diverged.[3] GitHub's SSH troubleshooting identifies “Permission denied” as a server rejection, but that guidance is SSH-specific and does not define all HTTPS/token failure diagnostics.[7]

When a push is rejected, fetch and inspect ancestry before changing the local branch; GitHub recommends fetching and integrating remote changes before trying again.[1] Choose an explicit integration policy and stop with local commits preserved if an unexpected conflict needs review.[unverified] Git documents that rebase `--abort` returns to the original branch state, whereas `--skip` omits the conflicting commit.[4] Do not use force-push as an automatic response to a rejected update: Git documents force updates as overriding the normal non-fast-forward protection.[2]

Kubernetes `git-sync` distinguishes initial and steady-state synchronization and documents consecutive-failure limits and an optional error file.[5] Flux exposes source readiness and the resolved artifact revision.[6] These are pull-side systems, so they demonstrate explicit failure and revision visibility—not a proven write-side divergence algorithm.

For an unattended writer, retaining last-success time, local and remote revisions, ahead/behind state, failure count, and last error is a design recommendation synthesized from these status patterns, not a universal documented schema.[unverified] Bounded retry/backoff for plausibly transient faults and stopping on authentication or integration failures are likewise policy recommendations; the cited products do not establish universal thresholds.[unverified] A conservative recovery principle is **preserve local commits, inspect fetched ancestry, integrate deliberately, and do not overwrite shared history automatically**.[1][2][4]

This is an engineering synthesis from Git semantics and pull-side controller patterns, not comparative evidence that one retry budget or divergence policy is best. Its effectiveness depends on branch topology, commit policy, and the quality of the alert path.[unverified]

## Sources

[1] https://docs.github.com/en/get-started/using-git/dealing-with-non-fast-forward-errors — GitHub: Dealing with non-fast-forward errors
[2] https://git-scm.com/docs/git-push — Git: git-push
[3] https://git-scm.com/docs/git-pull — Git: git-pull
[4] https://git-scm.com/docs/git-rebase — Git: git-rebase
[5] https://raw.githubusercontent.com/kubernetes/git-sync/master/README.md — Kubernetes git-sync v4 README
[6] https://fluxcd.io/flux/components/source/gitrepositories — Flux Git Repositories
[7] https://docs.github.com/en/authentication/troubleshooting-ssh/error-permission-denied-publickey — GitHub SSH permission denied publickey
