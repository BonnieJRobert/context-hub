---
name: pr-create
description: Guide for creating pull requests in this repository. Use when asked to open, create, or submit a PR.
license: Apache-2.0
---

# Creating Pull Requests

Follow this process when asked to create a pull request for changes made in
this repository.

## 1. Verify the working tree

- Run `git status` and `git --no-pager diff` to confirm the intended changes
  are present and nothing unrelated is staged.
- Confirm you are not on `main` or `master`; create or switch to a feature
  branch if needed.
- Use a short, descriptive, kebab-case branch name (for example,
  `update-agent-guidance`).

## 2. Commit the changes

- Stage only relevant files.
- Write a concise, imperative commit message (for example,
  `Update repository guidance`).
- Append this trailer unless the user explicitly asks otherwise:

  ```text
  Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>
  ```

## 3. Push the branch and open the PR

- Push with upstream tracking: `git push -u origin <branch-name>`.
- Prefer the `gh` CLI to create the pull request.
- Write the PR body to a temporary Markdown file and pass it with
  `--body-file`; avoid fragile inline shell quoting.
- Include a concise summary, key changes, and validation. Link related issues
  where applicable.
- Verify the created PR and share its URL.

Do not force-push unless explicitly requested.
