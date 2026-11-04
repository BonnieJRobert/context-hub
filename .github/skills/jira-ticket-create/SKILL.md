---
name: jira-ticket-create
description: Guide for creating a new JIRA ticket under a specified epic with a title tag and an assignee. Use when asked to create, open, file, or add a new JIRA ticket, issue, story, or task.
license: Apache-2.0
---

# Creating a JIRA Ticket

This skill extends `jira-ticket-edit`. Follow that skill for connecting to
JIRA (its section 1), understanding the ticket (2), structure (3), style and
wording (4), fields and metadata (5), issue type (6), and review (7). This
skill adds the inputs and steps specific to creating a new ticket, and where
it states a different default (issue type, priority), this skill wins.

If `jira-ticket-edit` is not available, apply these minimum rules: read the
connection from `JIRA_BASE_URL`, `JIRA_EMAIL`, and `JIRA_API_TOKEN` (check only
that they are set, never print them); write the description as a Context
paragraph followed by a Definition of Done; and show the user the proposed
ticket before creating it.

## 1. Required inputs

Collect all of these before creating anything. They may come from the user or
from another worker. Ask for any that are missing; never guess them.

| Input | Notes |
|-------|-------|
| Epic key | For example `ABC-123`. The team uses epics in place of JIRA projects, and projects in place of team boards, so the epic is the parent of the new ticket. Never assume a default epic. |
| Assignee | A person's name, email, or account ID. If the task is a GitHub pull request to review and none is provided, use the PR's reviewer (see section 2). Otherwise, if none is provided, ask. Do not leave the ticket unassigned or assign it to the user by default. An assignee the user provides always wins over the reviewer. |
| Task description | What the work is and why. Enough to write the Context and Definition of Done. |

Optional inputs: tag, priority, issue type, links.

- The JIRA project key (`ABC` in `ABC-123`) is taken from the epic key and is
  only used to create the ticket and look up assignees. Do not ask for it
  separately. If the user supplies a bare project key with no epic, ask for
  the epic.
- Tag: use the one provided. If none is provided, infer it by reading other
  tickets under the epic (for example, JQL `parent = <EPIC-KEY>`) and reusing
  their tag. If the epic has no tickets, or its tickets use different tags,
  ask. If a provided tag differs from the epic's other tickets, point this out
  and ask which to use.
- Ask for all missing inputs in a single question.

## 2. Resolve the assignee

JIRA assigns by account ID, not by name.

- If given an account ID, check that it is assignable in the project:

  ```bash
  curl -s -w '\nHTTP %{http_code}\n' \
    -u "$JIRA_EMAIL:$JIRA_API_TOKEN" -H "Accept: application/json" \
    "${JIRA_BASE_URL%/}/rest/api/3/user/assignable/search?project=<PROJECT>&accountId=<ACCOUNT-ID>"
  ```

  Success is HTTP 200; a 200 with an empty array (`[]`) means no assignable
  match, and any other status is an error (401 or 403 is an authentication or
  permission problem; report it without the credentials). If the result is
  empty or an error, do not create the ticket with that ID; tell the user it
  is not assignable and ask for another assignee.
- Otherwise look the person up and confirm the match:

  ```bash
  curl -s -w '\nHTTP %{http_code}\n' \
    -u "$JIRA_EMAIL:$JIRA_API_TOKEN" -H "Accept: application/json" \
    "${JIRA_BASE_URL%/}/rest/api/3/user/assignable/search?project=<PROJECT>&query=<name-or-email>"
  ```

  The same status rules apply: only HTTP 200 is success, and `[]` means no
  matches.
- Only users assignable in the project are valid. If there are no matches or
  several, show the candidates (display name only) and ask the user to choose.
- Do not store account IDs or email addresses in the repository.

### Assignee from a GitHub PR reviewer

When the task is a GitHub pull request and no assignee was provided, use the
person tagged as reviewer.

1. Read the reviewers (read-only):

   ```bash
   gh pr view <number-or-url> --json reviewRequests,latestReviews \
     --jq '{requested: [.reviewRequests[] | .login // .slug], reviewed: [.latestReviews[].author.login]}'
   ```

   - Ignore team entries (those with a `slug` rather than a `login`) and the
     PR author.
   - Use requested reviewers first. Use people who already reviewed only when
     nobody is requested.
   - If there are several reviewers, ask the user which one to assign. If
     there are none, ask for an assignee.
2. Map the GitHub handle to a JIRA user. Handles often do not match work
   emails, so infer the person rather than searching on the handle:
   - Read the public profile: `gh api users/<login> --jq '{name, email}'`.
   - Search JIRA assignable users using the profile name (full name, then
     last name), then the profile email if present, then the handle itself.
   - Accept a match only when the display name clearly corresponds to the
     profile name. Do not accept a match on the handle alone.
3. Always confirm an inferred assignee with the user before creating the
   ticket, showing the GitHub handle and the matched JIRA display name. If
   there is no clear match, show the candidates (display name only) and ask.
4. Do not store GitHub handles mapped to emails or account IDs in the
   repository.

## 3. Draft the ticket

- Title: `[<tag>] <short action phrase>`.
- Description: Context, then Definition of Done, as in `jira-ticket-edit`.
- Issue type: Task. Use Story only when the user explicitly asks for it.
- Priority: Low by default. Raise it only when the ticket blocks other work or
  a deadline, or when the user specifies one, and state the reasoning. Leave
  labels and components empty unless asked.
- Show the user the full draft (epic, issue type, title, description,
  assignee, priority) and get approval before creating. Note any inferred
  values, such as the tag.

## 4. Create the ticket

When the connection variables are set, create it with the REST API. The
description must be in Atlassian Document Format (ADF), not Markdown:

```bash
curl -s -w '\nHTTP %{http_code}\n' -u "$JIRA_EMAIL:$JIRA_API_TOKEN" \
  -H "Accept: application/json" -H "Content-Type: application/json" \
  -X POST "${JIRA_BASE_URL%/}/rest/api/3/issue" \
  --data @ticket.json
```

Minimal `ticket.json`; write it to a temporary file and delete it afterward:

```json
{
  "fields": {
    "project": { "key": "<PROJECT>" },
    "issuetype": { "name": "Task" },
    "summary": "[<tag>] <short action phrase>",
    "description": {
      "type": "doc",
      "version": 1,
      "content": [
        { "type": "paragraph", "content": [{ "type": "text", "text": "<Context>" }] },
        { "type": "paragraph", "content": [{ "type": "text", "text": "Definition of Done: <outcomes>" }] }
      ]
    },
    "assignee": { "accountId": "<ACCOUNT-ID>" },
    "parent": { "key": "<EPIC-KEY>" },
    "priority": { "name": "Low" }
  }
}
```

- `<PROJECT>` is the prefix of the epic key.
- Set `priority` to the chosen priority name.
- Check the HTTP status on the last line of the output; success is HTTP 201. A
  400 usually means a required field is missing or the issue type is not valid
  for the project; 401 or 403 means an authentication or permission problem.
  Report the error message, not the credentials.
- If any connection variable is missing, do not call JIRA. Give the user the
  drafted ticket text and say briefly that no JIRA connection was available.

## 5. Report

- Give the new ticket key and its URL (`<JIRA_BASE_URL>/browse/<KEY>`), its
  assignee's display name, and the epic.
- Do not include secrets, personal information, or unapproved proprietary
  material in the ticket.
