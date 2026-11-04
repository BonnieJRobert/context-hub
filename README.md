# context-hub

A personal library of reusable `SKILL.md`, `.cursorrules`, and other context
standards for AI coding tools and software projects.

## Repository layout

```text
.
├── .cursorrules
├── .gitignore
├── .github/
│   ├── agents/
│   │   └── github-project-manager.agent.md
│   ├── instructions/
│   ├── copilot-instructions.md
│   └── skills/
│       ├── document-table-to-r/SKILL.md
│       ├── pr-create/SKILL.md
│       ├── pr-review/SKILL.md
│       ├── repo-init/SKILL.md
│       └── repo-sync/SKILL.md
├── ARCHITECTURE.md
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## Getting started

Browse the files by format and purpose. Adapt a skill or instruction file to
the conventions of its destination repository before reusing it; keep each
document focused and portable.

The [document-table-to-r skill](.github/skills/document-table-to-r/SKILL.md)
supports PDF and Excel files. HTML, Markdown, and Word support has not been
tested.

## Porting a skill to another workspace or repository

To use one of these skills elsewhere, run the following from this repository's
root to copy the complete skill directory into the destination's
`.github/skills/` directory:

```bash
mkdir -p /path/to/your-repo/.github/skills
cp -R .github/skills/<skill-name> /path/to/your-repo/.github/skills/
```

To make a skill available across all of your repositories, copy it into your
personal skills directory (`~/.copilot/skills/`) instead; otherwise copy it
into each repository that needs it. Keep the directory name in lowercase
kebab-case, matching the `name` in the front matter, and retain the
`SKILL.md` filename and YAML front matter (`name` and `description`).

Before using the ported skill, review and adapt repository-specific paths,
commands, tools, package assumptions, permissions, and references to other
instructions. Remove examples or behavior that do not fit the destination's
policies. Then test it with a representative request and confirm that the
result follows the skill's required user choices, safety boundaries, and
output format.

## Conventions

- Use Markdown for human-readable guidance and keep examples portable.
- Put reusable Copilot agent profiles under `.github/agents/`.
- Put scoped Copilot instructions under `.github/instructions/`.
- Put GitHub Copilot skills under `.github/skills/<skill-name>/SKILL.md`.
- Keep compatibility guidance in `.cursorrules` concise and consistent with
  the other instruction files.
- Never add secrets, personal information, or proprietary content that is not
  approved for public release.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for contribution guidelines.

## License

Copyright 2026 Province of British Columbia

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
