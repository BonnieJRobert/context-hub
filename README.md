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
supports PDF and Excel files. HTML support has not been tested.

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
