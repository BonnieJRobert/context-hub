# context-hub

A personal library of reusable `SKILL.md`, `AGENTS.md`, `.cursorrules`, and
other context standards for AI coding tools and software projects.

## Repository layout

```text
.
├── AGENTS.md
├── .cursorrules
├── .gitignore
├── .github/
│   ├── agents/
│   ├── instructions/
│   ├── copilot-instructions.md
│   └── skills/
│       ├── pr-create/SKILL.md
│       ├── pr-review/SKILL.md
│       └── repo-sync/SKILL.md
├── .markdownlint-cli2.jsonc
├── ARCHITECTURE.md
├── CONTRIBUTING.md
├── LICENSE
├── package.json
└── README.md
```

## Getting started

Browse the files by format and purpose. Adapt a skill or instruction file to
the conventions of its destination repository before reusing it; keep each
document focused and portable.

Run the Markdown linter before submitting changes:

```bash
npm install
npm run lint:md
```

## Conventions

- Use Markdown for human-readable guidance and keep examples portable.
- Put reusable Copilot agent profiles under `.github/agents/`.
- Put scoped Copilot instructions under `.github/instructions/`.
- Put GitHub Copilot skills under `.github/skills/<skill-name>/SKILL.md`.
- Keep repository-wide agent guidance in `AGENTS.md`; keep
  `.github/copilot-instructions.md` aligned with it.
- Keep compatibility guidance in `.cursorrules` concise and consistent with
  the other instruction files.
- Never add secrets, personal information, or proprietary content that is not
  approved for public release.
- Run `npm run lint:md` after editing Markdown.

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
