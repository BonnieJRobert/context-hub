---
name: pr-review
description: Review pull requests or branch diffs for correctness, clarity, portability, and repository context-file conventions. Use when asked to review or give feedback on a PR or branch diff.
license: Apache-2.0
---

# Reviewing Pull Requests

Review the requested pull request or branch diff against its stated intent and
the repository's conventions. Post the findings to the PR as a comment after
every PR review; do not approve, request changes, or merge unless explicitly
asked.

## 1. Gather context

- Prefer the `gh` CLI for GitHub operations.
- If no PR is specified, list open PRs and ask the user to choose one. If there
  is only one open PR, confirm it; if none are open, say so and stop. For a
  branch without a PR, use `git --no-pager diff main...HEAD`.
- Read the PR description and diff, then consult `ARCHITECTURE.md` and the
  relevant instruction files, especially `.github/copilot-instructions.md`.
- Open changed files in full when needed to understand their context.
- For follow-up reviews, read earlier reviews and comments and compare the
  commits added since the last review.

## 2. Review the changes

Check for meaningful problems, including:

- Skill directories outside `.github/skills/` or skill files without valid
  `name` and `description` front matter.
- Instructions that conflict with one another or the stated purpose.
- Repository-wide guidance that conflicts across `AGENTS.md`,
  `.github/copilot-instructions.md`, and `.cursorrules`.
- Unsafe guidance, requests to expose secrets, or examples containing
  credentials, personal information, or unapproved proprietary content.
- Unclear, overly broad, non-portable, or contradictory directions.
- A missing `ARCHITECTURE.md` or `README.md` update when the repository layout
  changes.
- A PR description with an inaccurate file count, missing Summary/Key Changes/
  Validation sections, incorrect base or links, or validation that does not
  match what was run. Treat description discrepancies as observations, not
  defects, and never edit the PR description while reviewing.

Only report issues that matter; do not pad the review with style trivia. For
each finding, give the file and line, impact, and suggested correction. Group
findings as **Blocking**, **Should fix**, **Nit**, or **Of note** (observations
such as PR description discrepancies). Recheck and report the status of each
earlier finding in follow-up reviews. End with a recommendation: approve,
approve with nits, or request changes.

## 3. Validate and report

- Run relevant validation checks when practical; do not run checks that alter
  tracked files or push changes.
- Post a single review with the `COMMENT` event, including the findings and
  recommendation. Do not use `APPROVE` or `REQUEST_CHANGES` unless explicitly
  asked. Keep the posted comment focused on findings, not checks that passed.
- Offer small, mechanical nits as inline GitHub suggestions on changed lines;
  use plain comments for findings that need judgment. Get the PR head SHA as
  `commit_id`, and use its line numbers, not those of the working tree. Each
  inline suggestion needs `path`, `line`, `side: "RIGHT"`, and a `body` with a
  fenced `suggestion` block containing the complete replacement line. Add
  `start_line` for multi-line suggestions. Do not suggest on blank lines or
  lines outside the diff; put those observations in the review body instead.
- Write review JSON to a temporary file with file tools, submit it with
  `gh api repos/<owner>/<repo>/pulls/<number>/reviews --input <file>`,
  delete the file, and share the review URL. For follow-up reviews, note what
  has been resolved before reporting remaining or new findings.

Never modify the PR branch while reviewing.
