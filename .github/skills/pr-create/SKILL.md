---
name: pr-create
description: Guide for creating pull requests in this repository. Use when asked to open, create, or submit a PR, or push committed changes for review.
license: Apache-2.0
---

# Creating Pull Requests

Follow this process when asked to create a pull request for changes made in
this repository.

## 1. Verify the working tree

- Run `git status`, `git --no-pager diff`, and `git --no-pager diff --cached`
  to confirm the intended changes are present and nothing unrelated is staged.
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
- If upstream tracking already exists, a plain `git push` is sufficient.
- Prefer the `gh` CLI over MCP tools for GitHub operations.
- Write the PR body to a temporary Markdown file and pass it with
  `--body-file`; do not pass Markdown containing backticks inline in
  double-quoted shell arguments. Use file tools rather than shell interpolation
  to write the body.
- Before creating or editing the PR, count changed files so the opening line
  `Please review the (<count>) files changed in this PR.` is accurate.
- Include **Summary**, **Key Changes**, and **Validation** sections. Describe
  what changed, give meaningful validation results (or say none was needed),
  and link related issues where applicable. For issues in another repository,
  use `owner/repo#<number>`.
- For a stacked PR, link the earlier PR in the body.
- After creating or editing the PR, verify the rendered body with
  `gh pr view <number> --json body -q .body`, delete the temporary file, and
  share the PR URL. If `gh pr edit --body-file` fails with an unrelated
  Projects (classic) GraphQL error, use the REST API to update the body.

Do not force-push unless explicitly requested.
