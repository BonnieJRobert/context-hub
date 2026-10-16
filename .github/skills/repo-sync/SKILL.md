---
name: repo-sync
description: Resync the local clone with its remote and clean up stale branches safely.
license: Apache-2.0
---

# Resyncing and Cleaning Up a Repository

Follow this process when asked to synchronize a local clone or clean up stale
branches. Prefer `gh` for GitHub operations. Never force-push.

## 1. Check local state

- Run `git status --short` and `git branch --show-current`.
- If there are uncommitted changes, ask the user whether to commit, stash, or
  leave them. Do not discard them.
- Start from `main`. If another branch is checked out, check for unpushed
  commits or an open PR before switching.

## 2. Fetch and update

- Run `git fetch --prune`.
- Run `git pull --ff-only` on `main`.
- If fast-forward fails, stop and report the divergence; do not merge, rebase,
  or reset without permission.
- Summarize the commits pulled.

## 3. Identify stale branches

- Compare local and remote branches with PR state using `gh pr list --state all`.
- A branch is stale when its PR is merged, or when its remote branch is gone
  and its commits are on `main`.
- Ask before deleting a branch whose PR was closed without merging or whose
  status is otherwise ambiguous. Leave branches with unpushed work alone.

## 4. Clean up and verify

- Delete only confirmed stale branches; never delete `main` or the current
  branch.
- Run `git fetch --prune`, inspect branch listings, and verify with
  `git status -sb` that `main` matches `origin/main`.
- Report the update and any branches left in place.
