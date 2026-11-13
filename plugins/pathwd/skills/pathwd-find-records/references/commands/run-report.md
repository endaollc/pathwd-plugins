# run-report

Runs with: path_folders_reports(command: "run-report", args: "<flags>") (read-only).

```
Usage: run-report [options]

Run a report and print its rows (the web app's report view; in real-world mode
only reports the acting user can open): a table of record (the record id; total
on a group total row) and the report's own columns with the cells as the web
shows them, then a paging footer. Under the rows: the report's own context
(what it is for, how to read it), its business object's, each column's (the
column's own, else the context of the field it shows — through relationships,
the end field's, with the relationships on the path), and that of filter-only
Custom Fields (compared by the filter, not shown). Like all context, each is
shown once per session; later runs leave it out (pass --with-context to repeat)

Options:
  --report-id <id>      Report ID
  --report-name <name>  Report name (resolved to ID)
  --page <n>            Page number (default: "1")
  --per-page <n>        Rows per page (default: "50")
  --all                 Every row in one call (--page / --per-page are ignored)
  --user-filter <json>  Run-time values for the user filters (JSON array). Name
                        a ticked row by "label" (the label viewers see) or
                        "uid" (the "uid:" line under the row in get-filter
                        folder|report <id>); a milestone "currently in" user
                        filter takes milestone names or ids from its allowed
                        list, e.g.
                        '[{"label":"Stage","values":["Lead","Qualified"]}]'
                        (entity_id and logic are filled in; INMILESTONE /
                        NOTINMILESTONE rows take one; [] = row not applied).
                        Other rows:
                        '[{"label":"Type","logic":"equal","values":["Retail"]}]';
                        raw overrides such as
                        '[{"entity_id":12,"logic":"equal","values":["John"]}]'
                        pass through
  --include-archived    Include archived records (overrides the report's
                        setting)
```

## Examples

- args: `--report-id 42 --per-page 50`

## Notes

Runs a saved report and prints its rows with the report's columns.

**Examples**

```
run-report --report-name "Won this month"
run-report --report-id 2496 --all
run-report --report-id 2496 --user-filter '[{"label":"Salesperson","values":["Todd Demo"]}]'
run-report --report-id 2496 --include-archived
```

- 50 rows a page by default (`--per-page`, `--page`); `--all` returns every row.
- `--user-filter` fills the report's user filters for this run; `[]` for a row leaves it unapplied.
- Grouped reports return the groups with their totals.

**Output:** the rows as a table, then the context of what they show:

```
record                   | Vendor   | Purchase Order                    | Total of All Orders
-------------------------|----------|-----------------------------------|--------------------
total                    | Andersen |                                   | $152.00
6aa309fadfebb71bcf985730 | Andersen | SR26-00027-01: Palmer Donovan     | $150.00
6aa414e8b4114059b6a3e639 | Andersen | SR26-00027-02: Andersen           | $2.00
[2 of 11 rows + 1 group total — --page 2 for more]

Report context:
  Service Orders By Vendor: Open service purchase orders by vendor.
Column context:
  Total of All Orders: Sum of the order lines' sell price.
```

- Columns to read: `record` (the record id `get-record` takes; `total` on a group total row of a grouped report), then the report's own columns by name, each cell as the web's report view shows it (numbers with the column's formatting, dates on the user's clock). Long cells end in `…`.
- Footer: rows shown of the total, the group total rows, and the next page (`--page 2`) when there are more; `[0 rows]` when nothing matches (a report on `CURRENTUSER()` only shows the user's own records).
- Under the rows, once per session each: `Report context`, `Business object context`, `Column context` (a column's own, else its field's), `Relationship context (column paths)` and `Custom Field context` (filter-only Custom Fields).
- `--fields record,Vendor` picks columns by name; `--quiet` prints the record ids.

**Related:** `list-reports` (ids, names, context), `get-report`, `find-records --group-by` (a grouped question with no saved report), `create-report`.

Related commands: [`list-folders`](list-folders.md), [`list-reports`](list-reports.md), [`get-report`](get-report.md).
