# Mac app parser semantics

The Mac app (Issues.app) reads issue files via regex. This is what each rule looks for. Read this when you need to debug *why* a file isn't appearing or rendering correctly in the app — not for normal filing.

## Filename

Pattern: `^\d{4}\.md$`

Exactly 4 digits, then `.md`. Files like `0001a.md`, `001.md`, or `0001.markdown` are silently skipped. The id is the 4-digit string itself.

## Title

Pattern: `^# \d+ — (.+)$` (multiline mode, anchors match line starts)

The separator is an em-dash (U+2014, `—`). A hyphen (`-`) or en-dash (`–`) will not match and the issue will appear without a title.

## Metadata fields

Pattern: `\|\s*\*\*<NAME>\*\*\s*\|\s*(.+?)\s*\|`

For each of `Status`, `Module`, `Platform`, `First seen`, `Closed`, the parser scans for a row whose first cell is the field name in bold. Whitespace inside the cell is trimmed.

The metadata table doesn't need to have a header row, separator row, or any particular position — the parser scans for matching rows by name.

## Description

Pattern: `## Description\s+(.+?)(?=\n##|\Z)` (dot-matches-line-separators)

Captures everything from after `## Description` up to the next `## ` heading or end of file. Internal newlines are preserved. Leading and trailing whitespace is trimmed.

The Mac app shows the **first paragraph** of this in summary views, so structure the description with the punchline first.

## Dates

Format: `yyyy-MM-dd`, parsed with `Locale(identifier: "en_US_POSIX")` and UTC.

Anything else (`May 3, 2026`, `2026/05/03`, `26-05-03`, ISO timestamps with time-of-day) parses as missing. The Mac app falls back to "—" in the UI.

## Status normalization

The `Status` field value is lowercased and any internal whitespace is replaced with `-` before being matched against the enum. So:

- `Open`, `OPEN`, `open` → `open`
- `In Progress`, `in progress`, `In-Progress`, `in-progress` → `in-progress`
- `Won't Fix`, `Wontfix`, `wontfix` → `wontfix`

Unknown values fall back to `open`.

This is forgiving, but for diff cleanliness and consistency, write the canonical lowercase form (`open`, `in-progress`, `resolved`, `closed`, `wontfix`).

## What the Mac app *doesn't* parse

- Steps to reproduce, Expected behavior, Actual behavior, Attachments, Notes — these are stored as part of the file but not extracted into structured fields. They render when the user opens the detail panel (currently as plain text; future versions may render markdown).
- Image references in Attachments are not resolved by the v1 Mac app — opening the attached file is up to the user.
- Any sections beyond the metadata table and `## Description` are ignored for indexing/filtering purposes.

## Module splitting

The `Module` field is split on ` / ` (space-slash-space) for grouping in the swimlane view. The first segment is the "primary module"; the issue appears under that group's swimlane. All segments contribute to the module filter list.
