# get-report

Runs with: path_folders_reports(command: "get-report", args: "<flags>") (read-only).

```
Usage: get-report [options] <report-id>

Show a report: its business object and type, its columns (column id, name,
type, hidden, the COLUMN("name") a filter compares, a computed column's
definition), its filter-only Custom Fields (CUSTOMFIELD("name")), its filter as
formula text, its user filters, and the report's and each column's own context
(what it is for; once per session like all context)

Arguments:
  report-id   Report ID

Options:
```

## Examples

- args: `42`

## Notes

Shows how a saved report is built: its columns, its filter-only Custom Fields, its filter and its user filters, and the report's and each column's context. It doesn't return rows: `run-report` does.

**Examples**

```
get-report 3830
get-report 2542
```

- The report id is a positional argument, not a flag; `list-reports` gives ids.
- Needs what opening the report needs: View on its business object and the report shared with (or owned by) the user.

**Output**

```
report 3830 "Jobs Test Report" · Jobs (bo 5050) · detail
columns (column id, name [type unless a field], hidden):
  33619 Job Name
  33621 Total time [timespan] — filter: COLUMN("Total time") (time_span)
      time between field:Created Date and today
  33622 Job Items Doubled [custom_calculation] — filter: COLUMN("Job Items Doubled") (number)
      = [Total Qty of Job Items] * 2
      Job Items Doubled context: Twice the job's item count, for the doubling test.
custom fields:
  33663 ship time — CUSTOMFIELD("ship time") (time_span)
      time between field:Created Date and field:Est Ship Date
filter: INTHELAST([Created Date], DAYS(30)) AND COLUMN("Job Items Doubled") > 0 AND CUSTOMFIELD("ship time") > DAYS(5)
user filters: Job Items Doubled
report context: Jobs created in the last 30 days, for testing.
```

- Lines to read: the business object and report type (`detail` or `grouped`); each shown column (its column id, its name, `[type]` when it isn't a plain field column — `milestone`, `workflow_step`, `milestones_as_field`, `count`, `custom_calculation`, `timespan` — and `hidden`); a computed column's definition under it and the `COLUMN("…")` a filter compares it by.
- `custom fields:` the filter-only Custom Fields (`CUSTOMFIELD("…")`, `[unused]` when no filter rule compares one); `custom fields: (none)` when there are none.
- `filter:` the report's filter as a formula (`(none)` without one); `user filters:` the rules a person fills in when running it (`run-report --user-filter`).
- Context lines (the report's and each column's own) show once per session; none prints when there is none.

**Common errors**

- `No report with id 9999.`
- `<user> can't run report "…" — it hasn't been shared with them, as in the web app.` (or `they can't view its business object`).

**Related:** `run-report`, `get-filter report <id>`, `set-filter report <id>`, `update-report`, `create-custom-field`, `list-reports`.

Related commands: [`list-folders`](list-folders.md), [`list-reports`](list-reports.md), [`run-report`](run-report.md).
