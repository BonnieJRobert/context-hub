---
name: gh-task-breakdown
description: Break a goal for a solo project into a parent GitHub issue with sub-issues, label each with a worker, and add them to a GitHub Project. Use when asked to chunk up, decompose, plan, or assign tasks as GitHub issues.
license: Apache-2.0
---

# Breaking a Goal into GitHub Issues

Follow these instructions to turn one goal into a parent issue and a set of
small sub-issues that agents (workers) can pick up. Write each issue with the
`gh-issue-edit` skill's structure: a Context paragraph and a Definition of Done.

## 1. Prerequisites

- `gh auth status` succeeds. Adding items to a Project needs the `project`
  scope: `gh auth refresh -s project`.
- Identify the target repository (`owner/repo`) and, if used, the Project
  number and owner (`@me` for a personal account). Ask the user if unknown.
- If `gh` is unavailable, present the plan in the chat only.

## 2. Plan the breakdown

- Restate the goal in one sentence and ask for missing context.
- Split the goal into tasks that each have a single clear outcome and can be
  finished in one working session.
- Prefer vertical slices that deliver something checkable over layers.
- Note real dependencies between tasks; do not invent them.
- Choose a worker for each task (see section 3).
- Show the user the plan as a table (title, worker, depends on, Definition of
  Done) and get approval before creating anything.

## 3. Workers

Workers are labels named `worker:<role>`. Suggested starting set; the user may
rename or extend it:

| Label | Use for |
|-------|---------|
| `worker:research` | Gathering information, comparing options, summarizing |
| `worker:coder` | Writing or changing code |
| `worker:writer` | Documentation, notes, write-ups |
| `worker:review` | Checking finished work |

- Create missing labels once:

  ```bash
  gh label create "worker:coder" --description "Coding agent" --color 0E8A16
  ```

- Apply exactly one `worker:*` label per task.
- To hand a task to Copilot coding agent, assign it with
  `gh issue edit <number> --add-assignee "@copilot"`. Only do so when the user
  asks, because it starts work.

## 4. Create the issues

1. Create the parent issue for the goal, with a body containing the Context
   and a Definition of Done for the whole goal.
2. Create each sub-issue with `--body-file`, its labels, and a body that names
   any dependency (for example, `Depends on #12.`):

   ```bash
   gh issue create --title "<title>" --body-file <file> --label "worker:coder"
   ```

3. Link each sub-issue to the parent. The API needs the sub-issue's numeric
   id, not its number:

   ```bash
   id=$(gh api repos/<owner>/<repo>/issues/<child-number> --jq .id)
   gh api -X POST repos/<owner>/<repo>/issues/<parent-number>/sub_issues \
     -F sub_issue_id="$id"
   ```

4. Add the parent and sub-issues to the Project, if one is used:

   ```bash
   gh project item-add <project-number> --owner "@me" --url <issue-url>
   ```

5. Report the parent issue URL and a list of the sub-issues with their workers.

## 5. Keep the tracker current

- When a worker finishes a task, close its issue with a comment that states
  how the Definition of Done was met (`gh issue close <number> --comment ...`).
- To find the next task, list open sub-issues whose dependencies are closed,
  for example `gh issue list --label "worker:coder" --state open`.
- When all sub-issues are closed, check the parent's Definition of Done and
  close it.

## 6. Review before saving

- Get approval of the plan before creating issues, and show the final list
  afterward.
- Do not include secrets or personal information in issues.
