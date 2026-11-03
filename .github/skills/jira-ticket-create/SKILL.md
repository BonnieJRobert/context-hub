---
name: jira-ticket-create
description: Guide for creating a new JIRA ticket in a specified project with a title tag and an assignee. Use when asked to create, open, file, or add a new JIRA ticket, issue, story, or task.
license: Apache-2.0
---

# Creating a JIRA Ticket

This skill extends `jira-ticket-edit`. Follow that skill for connecting to
JIRA (its section 1), understanding the ticket (2), structure (3), style and
wording (4), fields and metadata (5), issue type (6), and review (7). This
skill adds the inputs and steps specific to creating a new ticket.

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
| Project key | For example `ABC`. Never assume a default project. |
| Tag | The bracketed title prefix, for example `pssm`. If none is provided, ask. Do not derive it. |
| Assignee | A person's name, email, or account ID. If none is provided, ask. Do not leave the ticket unassigned or assign it to the user by default. |
| Task description | What the work is and why. Enough to write the Context and Definition of Done. |

Optional inputs: parent epic key, priority, issue type, links.

- If a parent epic is given, read other tickets under it (for example, JQL
  `parent = <EPIC-KEY>`) and check that the supplied tag matches theirs. If it
  differs, point this out and ask which to use.
- Ask for all missing inputs in a single question.

## 2. Resolve the assignee

JIRA assigns by account ID, not by name.

- If given an account ID, use it as is.
- Otherwise look the person up and confirm the match:

  ```bash
  curl -s -u "$JIRA_EMAIL:$JIRA_API_TOKEN" -H "Accept: application/json" \
    "${JIRA_BASE_URL%/}/rest/api/3/user/assignable/search?project=<PROJECT>&query=<name-or-email>"
  ```

- Only users assignable in the project are valid. If there are no matches or
  several, show the candidates (display name only) and ask the user to choose.
- Do not store account IDs or email addresses in the repository.

## 3. Draft the ticket

- Title: `[<tag>] <short action phrase>`.
- Description: Context, then Definition of Done, as in `jira-ticket-edit`.
- Issue type: Story unless the user says otherwise.
- Priority: suggest one with brief reasoning as in `jira-ticket-edit`; the user
  decides. Leave labels and components empty unless asked.
- Show the user the full draft (project, issue type, title, description,
  assignee, parent, priority) and get approval before creating.

## 4. Create the ticket

When the connection variables are set, create it with the REST API. The
description must be in Atlassian Document Format (ADF), not Markdown:

```bash
curl -s -u "$JIRA_EMAIL:$JIRA_API_TOKEN" \
  -H "Accept: application/json" -H "Content-Type: application/json" \
  -X POST "${JIRA_BASE_URL%/}/rest/api/3/issue" \
  --data @ticket.json
```

Minimal `ticket.json`; write it to a temporary file and delete it afterward:

```json
{
  "fields": {
    "project": { "key": "<PROJECT>" },
    "issuetype": { "name": "Story" },
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
    "parent": { "key": "<EPIC-KEY>" }
  }
}
```

- Omit `parent` when there is no epic and `priority` when none was chosen
  (`"priority": { "name": "<Name>" }` otherwise).
- Check the HTTP status. A 400 usually means a required field is missing or the
  issue type is not valid for the project; report the error message, not the
  credentials.
- If any connection variable is missing, do not call JIRA. Give the user the
  drafted ticket text and say briefly that no JIRA connection was available.

## 5. Report

- Give the new ticket key and its URL (`<JIRA_BASE_URL>/browse/<KEY>`), its
  assignee's display name, and the project.
- Do not include secrets, personal information, or unapproved proprietary
  material in the ticket.
