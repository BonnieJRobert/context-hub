# Architecture

This document maps the repository and describes how its context files fit
together. Keep it current as the collection grows.

## Overview

`context-hub` is a personal library of reusable instructions and workflows for
AI coding tools. It stores portable context standards, with GitHub Copilot
skills in their recognized repository layout. Content is maintained as
documentation; there is no application runtime or service.

## Directory structure

| Path | Purpose |
| --- | --- |
| `AGENTS.md` | Repository-wide guidance for AI coding agents. |
| `.cursorrules` | Concise compatibility guidance for tools that consume this format. |
| `.gitignore` | Excludes local dependencies and editor/OS files. |
| `.github/copilot-instructions.md` | Repository instructions for GitHub Copilot. |
| `.github/skills/` | Reusable Copilot skills, each in its own directory with a `SKILL.md`. |
| `.markdownlint-cli2.jsonc` | Markdown lint rules and file selection. |
| `ARCHITECTURE.md` | This map of the repository and its conventions. |
| `CONTRIBUTING.md` | Contribution guidelines. |
| `LICENSE` | Apache License 2.0. |
| `package.json` | Markdown linter dependency and command. |
| `README.md` | Human-facing overview and getting-started guide. |

## Content flow

1. Add or update an instruction or skill in its canonical location.
2. Review it for clarity, portability, safety, and consistency with related
   guidance.
3. Run `npm run lint:md`.
4. Adapt the document for the target repository or tool when consuming it;
   context files are examples and reusable starting points, not universal
   policies.

## Conventions

- Use lowercase kebab-case directory names for skills.
- Store each Copilot skill in `.github/skills/<skill-name>/SKILL.md`.
- Give each skill valid YAML front matter containing `name` and `description`.
- Keep instructions actionable, scoped, and explicit about relevant safety
  boundaries.
- Avoid embedding credentials, personal information, or unapproved proprietary
  material in examples.
- Keep the root `AGENTS.md`, `.github/copilot-instructions.md`, and
  `.cursorrules` aligned when they describe the same repository-wide rules.
- Run the Markdown linter before submitting changes.
