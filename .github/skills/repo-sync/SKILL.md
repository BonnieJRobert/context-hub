---
name: repo-sync
description: Resync the local clone with its remote and clean up stale branches safely. Use when asked to sync, refresh, pull latest, or clean up branches after PRs are merged or closed.
license: Apache-2.0
---

# Resyncing and Cleaning Up a Repository

Follow this process when asked to synchronize a local clone or clean up stale
branches. Prefer `gh` for GitHub operations. Never force-push or delete work
that has not been merged without asking.

## 1. Check local state

- Run `git status --short` and `git branch --show-current`.
- If there are uncommitted changes, ask the user whether to commit, stash, or
  leave them. Do not discard them.
- Start from `main`. If another branch is checked out, check for unpushed
  commits or an open PR and tell the user about them before switching.

## 2. Fetch and update

- Run `git fetch --prune` to update remote-tracking references.
- Run `git pull --ff-only` on `main`.
- If fast-forward fails, stop and report the divergence; do not merge, rebase,
  or reset without permission.
- Summarize the commits pulled.

## 3. Identify stale branches

- Compare `git branch -vv`, `git branch -r`, and PR state from
  `gh pr list --state all --limit 200 --json number,headRefName,state`.
- A branch is stale when its PR is merged, or when its remote branch is gone
  and its commits are on `main`. Do not rely on `git branch --merged` alone:
  squash merges may not be recognized as merged commits.
- Ask before deleting a branch whose PR was closed without merging or whose
  status is otherwise ambiguous. Leave branches with unpushed work alone.
- A remote branch with no local counterpart is not automatically stale; it may
  belong to someone else. If it was not the head of a now-merged PR, treat it
  as someone else's work until proven otherwise.
  - Identify who has worked on it: `git log --format='%an <%ae> %ad' --date=short
    origin/main..origin/<branch>` for authors and dates, and
    `gh pr list --state all --head <branch> --json number,author,state` for
    any associated PR.
  - Report the branch, its authors, last commit date, and PR state to the user.
  - Always ask before deleting such a branch, even if the author is the user.
    Never delete it without explicit confirmation.

## 4. Clean up and verify

- Delete only confirmed stale branches; never delete `main` or the current
  branch. Use `git branch -D <branch>` only for a branch with a merged PR whose
  commits Git does not consider merged. Delete its remote branch with
  `git push origin --delete <branch>` only if the PR was merged, or if the
  user explicitly confirmed deletion after you reported who worked on it.
- Run `git fetch --prune`, inspect branch listings, and verify with
  `git status -sb` that `main` matches `origin/main`.
- Report local and remote branches deleted and any left active. Leave the user
  on `main` when finished.

Never rewrite history or run `git reset --hard` or `git clean`.
