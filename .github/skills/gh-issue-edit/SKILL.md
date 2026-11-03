---
name: gh-issue-edit
description: Guide for writing and editing GitHub issues for personal and solo projects. Use when asked to create, write, rewrite, refine, or edit a GitHub issue, task, or to-do in a personal repository.
license: Apache-2.0
---

# Writing and Editing GitHub Issues

Follow these instructions when writing or editing a GitHub issue for a solo
project, typically a personal-interest one.

## 1. Connect to GitHub

Use the `gh` CLI. Never hard-code a repository, owner, or token in this skill.

- Check authentication with `gh auth status`. Never print or ask for a token.
- Take the repository from the current git remote. If the working directory is
  not a clone of the target repository, ask the user for `owner/repo`.
- Read an issue with:

  ```bash
  gh issue view <number> --json number,title,body,labels,state,milestone
  ```

- Create and update with `gh issue create` and `gh issue edit`. Pass long
  bodies with `--body-file` rather than inline strings.
- If `gh` is unavailable or not authenticated, draft the issue text in the chat
  for the user to paste, and say briefly that no GitHub connection was
  available.
- Show the user the proposed change and get approval before any write to
  GitHub.

Setup (one time): install the GitHub CLI and run `gh auth login`. Projects need
an extra scope: `gh auth refresh -s project`.

## 2. Understand the issue

- Treat every issue as a single task: one unit of work with a clear outcome.
  Do not apply bug or user-story templates.
- Read the existing issue content before changing it.
- Read the parent issue (if any) for its context.
- Ask the user for missing context rather than guessing.

## 3. Structure

An issue has a title and a body. Write the body in two parts:

1. **Context:** one short paragraph on what exists now, why the work is
   interesting or needed, and what is wanted.
2. **Definition of Done:** the concrete, checkable outcomes that mean the work
   is finished.

Example body:

```text
The garden sensor readings are currently logged to a CSV by hand. I want to
log them automatically from the Raspberry Pi.

Definition of Done: A script on the Pi appends a reading every 15 minutes.
It has run unattended for 24 hours without gaps.
```

- For a small issue, write the Definition of Done as a single line starting
  with `Definition of Done:`.
- For a larger issue, write it as a bullet list under the same label.
- Each outcome must be verifiable by checking something concrete (for example,
  "the script is committed to the repo", not "logging works well").

## 4. Style and wording

- Title format: a short action phrase that starts with a verb or clear noun
  phrase, for example `Log sensor readings automatically`.
- Do not add a tag prefix unless the user's repository already uses one;
  follow the existing issue titles if so.
- Use plain prose. Do not use headings, user-story phrasing ("As a..."), or
  filler.
- Spell out acronyms on first use.

## 5. Fields and metadata

- **Parent:** link the issue to its parent issue as a sub-issue whenever one
  applies.
- **Priority:** suggest a priority and explain the reasoning briefly; the user
  decides. For personal work, treat it as a lower-stakes signal:
  - raise it if other work is blocked by this issue;
  - lower it if it is exploratory or has no deadline.
- **Labels, milestone, and project:** leave empty unless the user asks.
- **Assignee:** leave empty unless the user asks. Assign to workers with the
  `gh-task-breakdown` skill.
- **Links:** reference other issues (`#<number>`) only when a real dependency
  exists.

## 6. Issue type

- Create issues as plain tasks. Use a different issue type or label only when
  the user says so. Do not change an existing issue's type.

## 7. Review before saving

- Show the user the proposed changes before applying them.
- Preserve existing content unless the user asks to remove it.
- Do not include secrets or personal information.
