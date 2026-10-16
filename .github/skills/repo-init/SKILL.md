---
name: repo-init
description: Create a new R analysis repository with standard licensing, documentation, Copilot skills, and data-safe scaffolding. Use when asked to initialize or bootstrap an R analysis repository.
license: Apache-2.0
---

# Initializing an R Analysis Repository

Create a new GitHub repository for R analysis work on any topic. Work only in
the new repository; do not change this context library or the reference
repository. Prefer `gh` for GitHub operations and file tools for creating
files. Keep edits surgical and reviewable.

## 1. Gather inputs

Ask the user for:

- Repository name (lowercase kebab-case), one-line description, and analysis
  topic or domain.
- Owner or organization and visibility (public or private). For a `bcgov`
  repository, first confirm the required organization access, 2FA, repository
  administrator, and Content Approval Checklist.
- An expanded absolute parent directory for the clone. Do not assume the
  current repository is the destination; never nest a new clone inside it.
- Copyright year (default: current year) and license type: Apache-2.0 for a
  code repository (default), or CC BY 4.0 for documentation-only work.

## 2. Create the repository

Validate that the parent exists, is absolute, is not in another Git working
tree, and does not already contain a destination of the same name (including
a symbolic link). In Git Bash on Windows, expand `$HOME` and use a Git Bash
absolute path rather than a literal `~` or a relative path.

```bash
parent_dir="<expanded-absolute-parent-dir>"
[[ "$parent_dir" = /* && -d "$parent_dir" ]] || {
  printf 'Parent must be an existing absolute directory\n' >&2
  exit 1
}
parent_dir=$(cd -P -- "$parent_dir" && pwd)
if git -C "$parent_dir" rev-parse --is-inside-work-tree >/dev/null 2>&1; then
  printf 'Parent directory is inside a Git working tree\n' >&2
  exit 1
fi
if [[ -e "$parent_dir/<name>" || -L "$parent_dir/<name>" ]]; then
  printf 'Clone destination already exists\n' >&2
  exit 1
fi
gh repo create <owner>/<name> --<public|private> --description "<description>"
git clone https://github.com/<owner>/<name>.git "$parent_dir/<name>"
```

Create the first scaffold commit on `main`; if the empty repository starts on
`master`, run `git branch -M main` in the new clone before committing.

## 3. Add the scaffold

1. Add `LICENSE` first: Apache 2.0 from
   `https://www.apache.org/licenses/LICENSE-2.0.txt`, or CC BY 4.0 for a
   documentation-only repository.
2. Add `README.md` with the title, purpose, layout, getting started,
   conventions, contributing link, and license attribution. For Apache 2.0,
   include the standard license notice with the chosen year and copyright
   holder; do not assume the holder is the same for every new repository.
3. Add `CONTRIBUTING.md` with review and licensing expectations, R conventions,
   and a prohibition on committing secrets, personal information, or
   third-party data. If using a contribution template, adapt it to the owner.
4. Add `ARCHITECTURE.md` and `.github/copilot-instructions.md` reflecting
   the new repository's topic, owner, license, and directory layout. Describe
   its actual R and data conventions rather than copying instructions from
   another repository.
5. Copy `pr-create`, `pr-review`, and `repo-sync` into `.github/skills/` from
   `BonnieJRobert/context-hub`, adapting their scope to the new repository.
   Match each skill's `license` front matter to the destination's license and
   make examples generic. Do not copy `repo-init` unless the user requests it.
6. Add `.gitignore` entries for R session files, editor and OS files,
   `renv/` caches, `data/raw/*`, `data/processed/*`, and `scratch/`. Allow
   `.gitkeep` under both data directories.
7. Create `R/fxns/.gitkeep`, `data/raw/.gitkeep`, and
   `data/processed/.gitkeep`. Keep scripts and notebooks under `R/`, pure
   reusable helpers under `R/fxns/`, original datasets under `data/raw/`,
   derived datasets under `data/processed/`, and untracked notes under
   `scratch/`. `scratch/` needs no placeholder.

List only the skills actually copied in the destination's `README.md` layout
and `ARCHITECTURE.md` map.

## 4. Encode R conventions

- Use project-relative paths through `here::here()`, never `setwd()` or
  absolute paths.
- Use the native pipe `|>` rather than `%>%`; format with `air format .`.
  Air is a formatter, not a linter; ask before adding a linter.
- Start each `.R` file with the applicable Apache 2.0 copyright header when
  the destination uses that license.
- Use typed readers such as `readr::read_csv()` with parsing assumptions,
  `withr::with_seed()` for stochastic steps, `ggplot2` plots with labeled
  axes and units, and `lower_snake_case` names.
- Use bash syntax for shell commands and update `ARCHITECTURE.md` when the
  directory layout or data flow changes.

## 5. Verify, commit, and push

- Check that `git status` contains only the expected files. Use
  `git check-ignore -v` to verify raw data, processed data, and scratch files
  are ignored.
- Search for stale references to other repositories or unrelated topics
  before committing. Check that no secrets or data files are included.
- Commit with a concise imperative message and the trailer
  `Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>`,
  then push `main`. Report the repository URL and a brief summary.

Do not force-push. Do not add `COMPLIANCE.yaml`, `SECURITY.md`, or
`CODE_OF_CONDUCT.md` without confirming that the destination needs them.
