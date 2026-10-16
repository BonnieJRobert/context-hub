---
name: pr-review
description: Review pull requests or branch diffs for correctness, clarity, portability, and repository context-file conventions.
license: Apache-2.0
---

# Reviewing Pull Requests

Review the requested pull request or branch diff against its stated intent and
the repository's conventions.

## 1. Gather context

- Prefer the `gh` CLI for GitHub operations.
- If no PR is specified, list open PRs and ask the user to choose one. If none
  are open, say so and stop.
- Read the PR description and diff, then consult `ARCHITECTURE.md` and the
  relevant instruction files.
- Open changed files in full when needed to understand their context.

## 2. Review the changes

Check for meaningful problems, including:

- Instructions that conflict with one another or the stated purpose.
- Skill files in the wrong location or with invalid/missing `name` and
  `description` front matter.
- Unsafe guidance, requests to expose secrets, or examples containing
  credentials, personal information, or unapproved proprietary content.
- Unclear, overly broad, non-portable, or contradictory directions.
- A missing `ARCHITECTURE.md` update when the structure or organization changes.
- A PR description that inaccurately describes the changes or validation.

Only report actionable findings. For each, identify the file and line, explain
the impact, and suggest a correction. Do not pad the review with style trivia.

## 3. Validate and report

- Run `npm run lint:md` when practical; do not run checks that alter tracked
  files or push changes.
- Report findings by severity, with a brief recommendation.
- Do not approve, request changes, merge, or post a review unless the user
  explicitly asks. When asked to post a review, use a comment by default.

Never modify the PR branch while reviewing.
