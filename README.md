# renovate-queue-automerge

Minimal repository for reproducing Renovate automerge behavior with GitHub merge queues.

## What this repo is for

This repo is intended to exercise Renovate PR automerge when the base branch has a GitHub merge queue and code-owner review requirements.

The interesting config is in `renovate.json5`:

- `platformAutomerge: false` makes Renovate perform PR automerge itself instead of enabling GitHub-native auto-merge.
- `rebaseWhen: 'auto'` is the default behavior under investigation. In Renovate 44.73.0+, merge-queue-enabled base branches cause this to resolve to `conflicted` before `automerge=true` can resolve it to `behind-base-branch`.
- Patch npm updates are automerge-enabled.

## Suggested GitHub setup

To reproduce the problematic behavior, configure the default branch with a ruleset or branch protection that has:

- a merge queue enabled
- code-owner review required
- Renovate allowed to direct-merge/bypass normal PR requirements, if testing the direct-merge path
- Renovate not allowed to satisfy queue-entry approval requirements, if testing the enqueue failure path

Install/configure Renovate for this repository, then let it open a patch update PR for `lodash`.

Expected failure mode for the queue path:

```json
{
  "path": ["enqueuePullRequest"],
  "message": "Pull request Waiting on code owner review from psalaberria002/renovate-queue-automerge."
}
```
