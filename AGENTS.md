# Agent guidance

Read `ARCHITECTURE.md` first to understand the purpose and organization of this
repository.

## Repository purpose

This repository contains reusable context files and workflows for AI coding
tools, including `SKILL.md`, `AGENTS.md`, `.cursorrules`, and related standards.
Content is documentation; there is no application runtime.

## Where things go

- Repository-wide agent guidance: `AGENTS.md`
- GitHub Copilot repository instructions: `.github/copilot-instructions.md`
- Copilot skills: `.github/skills/<skill-name>/SKILL.md`
- Cursor compatibility guidance: `.cursorrules`
- Repository maps and contributor guidance: `ARCHITECTURE.md` and
  `CONTRIBUTING.md`

## Writing guidance

- Keep each document focused, actionable, and portable across repositories.
- Use lowercase kebab-case for skill directory names.
- Give every skill YAML front matter with `name` and `description`.
- Align overlapping guidance across the root agent file, Copilot instructions,
  and Cursor rules.
- Use fenced code blocks with an appropriate language where practical.
- Never add secrets, personal information, or unapproved proprietary content.
- Update `ARCHITECTURE.md` when adding a top-level directory or changing how
  context files are organized.

## Validation

Run `npm run lint:md` to lint Markdown files before submitting changes.
