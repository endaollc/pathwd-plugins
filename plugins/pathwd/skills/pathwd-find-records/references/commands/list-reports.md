# list-reports

Runs with: path_folders_reports(command: "list-reports", args: "<flags>") (read-only).

```
Usage: list-reports [options]

List the reports the user can open: id, business object, grouped, name. Under
the table: each report's context (what it is for, how to read it; once per
session like all context). get-report <id> shows a report's columns and filter;
run-report --report-id <id> its rows

Options:
  --page <n>      Fetch only this single page (default: all reports)
  --per-page <n>  Items per page (single-page mode) (default: "50")
  --all           Fetch all reports (this is now the default)
```

## Notes

Lists the reports the user can open, across every business object, with each report's context. It doesn't run them: `run-report` does.

**Examples**

```
list-reports
list-reports --page 2 --per-page 25
```

- Every report by default (all pages); `--page` / `--per-page` fetch one page instead. There is no business object filter: read the `bo` column to pick the right one.
- Only reports the user can open: View on the business object and the report shared with them or owned by them (Admins and report admins see every report).
- Report names are unique per business object; two business objects can each have a "Jobs Won - YTD".

**Output:** a table, then each report's context:

```
id   | bo       | grouped | name
-----|----------|---------|----------------------------------
2496 | Jobs     | yes     | Jobs Pipeline
2497 | Jobs     |         | My Jobs Pipeline
6041 | Projects |         | ZZ Report Info Check
[77 reports]

Report context:
  ZZ Report Info Check (6041): Live check report. Not real data.
```

- Columns to read: `id` (what `run-report --report-id`, `get-report` and `update-report` take), `bo` (the business object's name), `grouped` (`yes` for a grouped report; blank is a detail report), `name`.
- Context: the report's own (what it is for, how to read it), once per session.
- A report's columns, Custom Fields and filter: `get-report <id>`. One page (`--page`): `[50 of 77 reports — --page 2 for more]`.

**Related:** `get-report` (columns, Custom Fields, filter as a formula), `run-report`, `create-report`, `update-report`, `web-link report <id>`.

Related commands: [`list-folders`](list-folders.md), [`get-report`](get-report.md), [`run-report`](run-report.md).
