---
name: jira-ticket-edit
description: Guide for writing and editing JIRA tickets. Use when asked to create, write, rewrite, refine, or edit a JIRA ticket, issue, story, bug, or task.
license: Apache-2.0
---

# Writing and Editing JIRA Tickets

Follow these instructions when writing or editing a JIRA ticket.

## 1. Connect to JIRA

Never hard-code a JIRA site, project, or credential in this skill. Read the
connection from the environment:

| Variable | Purpose |
|----------|---------|
| `JIRA_BASE_URL` | Site URL, for example `https://<site>.atlassian.net` |
| `JIRA_EMAIL` | Atlassian account email |
| `JIRA_API_TOKEN` | Atlassian API token |

- Check only whether each variable is set, for example
  `[ -n "$JIRA_API_TOKEN" ] && echo set`. Never print, log, echo, or write
  values to a file, and never ask the user to paste a token into the chat.
- Take the project key from the ticket key the user provides (for example,
  `ABC-123` means project `ABC`). If none is given, ask the user.
- When all three variables are set, use the REST API with `curl`:

  ```bash
  curl -s -u "$JIRA_EMAIL:$JIRA_API_TOKEN" -H "Accept: application/json" \
    "${JIRA_BASE_URL%/}/rest/api/3/issue/<KEY>"
  ```

  - Request only the fields needed (for example, `?fields=summary,description,issuetype,status`).
  - Updates use `PUT /rest/api/3/issue/<KEY>`; the description must be in
    Atlassian Document Format (ADF), not Markdown.
  - Check the HTTP status and report failures (401 or 403 usually means a bad
    or expired token or missing permission) without echoing credentials.
- If any variable is missing, do not call JIRA. Draft the ticket text in the
  chat for the user to paste, and say briefly that no JIRA connection was
  available. Point the user to the setup steps below if they want live access.
- A JIRA MCP server may be used instead if it is connected to the same site.
  Prefer it only when it can reach the site the user is working in.
- Show the user the proposed change and get approval before any write to JIRA.

Setup (one time, from bash; then restart the terminal and the CLI so they
inherit the variables):

```bash
setx.exe JIRA_BASE_URL "https://<site>.atlassian.net"
setx.exe JIRA_EMAIL "<account-email>"
setx.exe JIRA_API_TOKEN "<token>"
```

Create the token at https://id.atlassian.com/manage-profile/security/api-tokens.
Do not store the token in a repository.

## 2. Understand the ticket

- Treat every ticket as a single task: one unit of work with a clear outcome.
  Do not apply bug or user-story templates.
- Read the existing ticket content before changing it.
- Read the parent epic (if any) for its tag and context.
- Ask the user for missing context rather than guessing.

## 3. Structure

A ticket has a title and a description. Write the description in two parts:

1. **Context:** one short paragraph on what exists now, why the work is needed,
   and what is wanted.
2. **Definition of Done:** the concrete, checkable outcomes that mean the work
   is finished.

Example description:

```text
In earlier years there has been some preprocessing work to do after loading
the credential data into SQL Server. We want to move this workflow to R.

Definition of Done: A series of scripts in R that simulate the 2019 workflow.
QA against the 2019 model data has been done.
```

- For a small ticket, write the Definition of Done as a single line starting
  with `Definition of Done:`.
- For a larger ticket, write it as a bullet list under the same label.
- Each outcome must be verifiable by someone else (for example, "the load
  script is in the GitHub repo", not "data is handled").

## 4. Style and wording

- Title format: `[<tag>] <short action phrase>`, for example
  `[pssm] Move credential analysis into R`.
- Take `<tag>` from the parent epic. Tags are per epic, so every ticket under
  an epic uses the same tag. There is no derivation rule: read the titles of
  other tickets under the same epic (for example, JQL `parent = <EPIC-KEY>`)
  and reuse their tag. If the epic has no other tickets, ask the user.
- Keep titles brief and start the phrase with a verb or clear noun phrase.
- Use plain prose. Do not use headings, user-story phrasing ("As a..."), or
  filler.
- Spell out acronyms on first use unless the team already uses them in the
  epic.

## 5. Fields and metadata

- **Parent:** link the ticket to its epic whenever one applies.
- **Priority:** suggest a priority and explain the reasoning briefly; the user
  decides. Base the suggestion on:
  - the parent epic's priority as the starting point;
  - whether other work or a deadline is blocked by this ticket (raise);
  - whether it is routine, exploratory, or has no deadline (lower).
- **Labels and components:** leave empty unless the user asks.
- **Links:** add issue links only when a real dependency exists.

## 6. Issue type

- The team's tickets are created as Story in JIRA. Use that type unless the
  user says otherwise. Do not change an existing ticket's type.

## 7. Review before saving

- Show the user the proposed changes before applying them.
- Preserve existing content unless the user asks to remove it.
- Do not include secrets, personal information, or unapproved proprietary
  material.

