# Copilot instructions

Read `ARCHITECTURE.md` first for the repository map and content conventions.

This repository is a personal library of reusable context files and workflows
for AI coding tools. It contains Markdown guidance rather than an application
or service.

## Content locations

- `.github/agents/` — reusable GitHub Copilot agent profiles
- `.github/copilot-instructions.md` — GitHub Copilot repository instructions
- `.github/instructions/` — scoped GitHub Copilot instructions
- `.github/skills/<skill-name>/SKILL.md` — individual Copilot skills
- `.cursorrules` — concise compatibility guidance
- `ARCHITECTURE.md` — repository map and content flow

## Editing standards

- Keep guidance focused, actionable, and portable.
- Put reusable agent profiles in `.github/agents/` and scoped instructions in
  `.github/instructions/`.
- Use lowercase kebab-case for skill directory names.
- Give each skill valid YAML front matter with `name` and `description`.
- Keep overlapping rules aligned across the repository's instruction files.
- Do not include secrets, personal information, or unapproved proprietary
  material.
- Update `ARCHITECTURE.md` when the structure or organization changes.
