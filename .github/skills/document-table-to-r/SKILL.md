---
name: document-table-to-r
description: Inspect a PDF, HTML, Markdown, Word, or linked Excel document at a URL, let the user choose a results table or worksheet, and generate a reproducible R script that downloads the data and preserves its context.
license: Apache-2.0
---

# Extracting a Document Table into R

Use this workflow when asked to turn tabulated results in a document at a URL
into an R data frame. Do not select a table for the user. Do not generate the
script until the user has chosen a table after seeing its meaning and
limitations.

## 1. Inspect the source

- Ask for the document URL if it is not supplied. Check that it is reachable
  and identify its actual format (PDF, HTML, Markdown, DOCX, older DOC, or
  Excel), rather than trusting its extension. Ask for access only through
  approved means if the document or linked data requires authentication;
  never embed credentials in the script or repository.
- Inspect the whole document, including appendices and supplementary tables.
  For PDF, inspect text and table structure across pages. For HTML, inspect
  semantic tables (including header and merged cells), their surrounding
  headings and notes, and relative links; distinguish layout/definition
  tables from results. For Markdown, inspect pipe/HTML tables and relative
  links. For DOCX, inspect tables and
  nearby paragraphs/captions; convert older DOC only if a suitable converter
  is available. If a conversion or parser is unavailable, say what is needed
  rather than claiming the file was scanned.
- Find tables of results, whether embedded or available through a linked
  CSV, spreadsheet, or other structured data file. Resolve relative links
  against the document URL, verify the target, and match linked data to its
  described table rather than treating every link as a result. Distinguish
  a data file from a link to another document. Follow relevant table
  references to find their associated footnotes and methodological context.
- For downloadable Excel workbooks (.xls or .xlsx), list the workbook links
  on the specified page with their descriptive link text and destination.
  If there are multiple workbook links, ask the user which one to use before
  proceeding. If the specified URL itself is a workbook, treat it as the
  single candidate. Even if there is only one, ask explicit permission to
  download the selected file, showing its resolved URL. Do not download or
  open the workbook if permission is declined.
- After permission, download the workbook to `tmp/data/` (relative to the
  destination repository root) and confirm it is a readable Excel file.
  Use `readxl` by default. Check
  `requireNamespace("readxl", quietly = TRUE)` before opening it; if absent,
  ask whether to install it locally. On approval, run
  `install.packages("readxl", repos = "https://cloud.r-project.org")` and
  confirm it loads. If declined or installation fails, explain and stop.
  Never install packages without this approval.
- Use `readxl::excel_sheets()` to list all worksheets. For each sheet, show
  its name and a small sample (for example, the first five rows using
  `readxl::read_excel(path, sheet = name, n_max = 5)`), noting blank title
  rows, merged headers, or metadata where they affect interpretation.
  If previews cannot be read, report the error rather than guessing. Ask the
  user which sheet to import, even if there is only one. Do not infer the
  worksheet from its position or silently choose the first.
- For image-only PDFs or image-based tables that cannot be read as text,
  report that OCR is required. Do not infer or transcribe values from an
  unreliable extraction. If no readable results tables exist, explain why
  and stop.

## 2. Present choices with context

For every candidate, identify its caption or descriptive heading, table
number (if any), page/section or link, what the rows and columns represent,
units, time and geographic coverage, and whether the data is embedded or
linked. Describe inclusion/exclusion criteria, denominators, filters,
conditions, suppressed or missing values, caveats, and relevant footnotes.
Distinguish facts stated in the document from uncertain interpretations.
If there are many tables, group or paginate them without omitting choices.

Ask the user to choose one table (or Excel worksheet) using `ask_user` when
available. Include its meaning and material restrictions in the choices; do
not offer only opaque table numbers or sheet indices. For Excel, make this
choice after displaying worksheet previews, and record the selected sheet
name. Save the generated R script under `tmp/` in the destination repository by
default; create that directory if needed. If the user specifies another
output path, use it. If the choice is ambiguous, clarify it before proceeding.
After a worksheet is selected, read that sheet into R with `readxl`,
preserving meaningful headers and documenting any skipped metadata rows.
Check the full result for unexpected blank or malformed columns before
generating a reproducible script.

## 3. Generate the R script

- Start each generated R script with a short Apache License 2.0 comment
  header, including `SPDX-License-Identifier: Apache-2.0` and a link to
  `https://www.apache.org/licenses/LICENSE-2.0`. Include a copyright
  notice only when the destination repository identifies an authorized
  holder; do not invent one or paste the full license into the script.
  Keep source-data attribution and restrictions separate from the script's
  license header.
- Use the selected table's verified source URL: download the document for an
  embedded table, or the linked data file for a linked table. Resolve any
  relative URL before embedding it in the script. Make the script runnable
  again from a clean R session; do not rely on files from the inspection
  session, manual copy/paste, or unpublished local paths. Generated scripts
  should create `tmp/data/` relative to the repository working directory,
  download source data there, and read it from there rather than using
  `tempfile()` or downloading into `tmp/` alongside the script. State that
  scripts are run from the repository root so the relative paths resolve.
  For an Excel file,
  tell the user that running the generated script will download the selected
  URL again; do not run that script without download permission.
- Choose parsing tools appropriate to the confirmed format and structure.
  For example, use a typed reader for CSV, `readxl::read_excel()` for the
  named Excel worksheet, specifying `sheet`, `range` or `skip`, and column
  types as needed after inspecting its headers and data;
  `rvest::read_html()` with `rvest::html_elements()` and
  `rvest::html_table()` for HTML, a Word table reader for DOCX, or
  `pdftools::pdf_text(..., raw = TRUE)`
  for text-based PDF when it preserves the selected table's rows. Prefer
  R packages over external executables for a reproducible R workflow.
  For Markdown, parse the selected table from downloaded text. If older DOC
  needs conversion, specify and check the converter. Avoid installing
  dependencies without permission; state required packages and system tools
  and fail clearly when they are missing. Generated scripts should check for
  `readxl` and fail with installation instructions, not install it on each run.
- Identify the table by its caption, heading, or other stable document
  context as well as its location. Do not silently use the first extracted
  table or assume table indices and page layouts never change. For HTML,
  disambiguate repeated tables and verify row labels and column headings
  before parsing; do not rely on the table's ordinal position alone. Preserve
  headers, row labels, units, footnotes needed to interpret values, and
  missing/suppression markers; do not turn suppressed cells into zero.
- For Excel, verify the selected worksheet name and expected headings before
  importing the full sheet. Treat note rows and footnotes as context, not
  observations; document any skipped rows and applied ranges in the script.
- Assign the parsed table to a named data frame (or tibble) with typed columns
  and leave it available after sourcing the script. Include R
  comments stating the document and data URLs, title, table identifier and
  location, access date or source version, population, coverage, units,
  inclusion/exclusion criteria, filters, conditions, denominators,
  limitations, and footnotes that affect interpretation. Mark unknown
  details as unknown rather than inventing them. Put any chosen filters in
  executable code as well as comments, and distinguish source filters from
  filters applied by the script.
- Check downloads and parsing explicitly. Reject unexpected structure,
  missing columns, or failure to identify the selected table; do not return
  an empty or apparently successful data frame on error. Keep downloaded
  source data in `tmp/data/` and ensure the entire `tmp/` directory is
  ignored by Git; do not commit generated scripts or downloaded data.

## 4. Verify and deliver

When feasible, run the generated script and compare its columns, representative
values, row count, units, and footnotes with the selected source table. Check
that documented restrictions match the source and any applied code filters.
If execution or extraction cannot be verified (for example, due to missing
tools, authentication, or changed layout), report the exact blocker and do not
present an unverified extraction as complete. Deliver the script path and a
concise description of its output and material limitations. Do not commit
downloaded documents or data unless the user explicitly requests it.
