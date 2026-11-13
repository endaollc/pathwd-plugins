# create-report

Runs with: path_manage_folders_reports(command: "create-report", args: "<flags>") (change).

```
Usage: create-report [options]

Create a report the way the web report builder does: columns (fields,
Relationship.Field, milestone/step dates, Milestones, Count,
CALC(<expression>), TIMEBETWEEN(<from>, <to>)), Group / Calculation columns, a
Logic Builder filter, Custom Fields, context. You become its owner; --users /
--roles share it. Needs Create Reports on the business object.

Options:
  --bo-id <id>                Business object ID
  --bo-name <name>            Business object name (resolved to ID)
  --name <name>               Report name (unique on the business object)
  --columns <specs>           Columns, in order, separated by commas: <source>
                              [| option …]. Sources: Field, Relationship.Field
                              ([…] for names with commas), milestone:<name>,
                              step:<name>, milestones, count,
                              CALC(<expression>), TIMEBETWEEN(<from>, <to>)
                              with from/to = today | field:<date field> |
                              milestone:<name> | step:<name>, or the same on a
                              related record through to-one relationships
                              (first related record), e.g. milestone:Order
                              Related PO.Received. Options: name=<title>,
                              context=<text>,
                              group[=day|week|month|quarter|year|<range>|<1d,
                              12h…>], total|average|minimum|maximum,
                              sort=asc|desc, width=<n>,
                              occurrence=first|most_recent, from-occurrence=,
                              to-occurrence=, group-expression=<TOTAL([x])…>,
                              format=<date format>, decimals=<n>, commas,
                              currency[=<symbol>], percent,
                              duration=days+hours+minutes+seconds
  --column <spec>             One more column (repeatable; same spec as
                              --columns, handy for CALC expressions with
                              commas)
  --custom-field <spec>       A filter-only Custom Field (repeatable):
                              CALC(<expression>) | name=<n> or
                              TIMEBETWEEN(<from>, <to>) | name=<n> — never
                              shown, compared in --logic with
                              CUSTOMFIELD("<n>")
  --type <type>               Report type: detail | grouped (the web picks it:
                              grouped once a column is a Group column); checked
                              against the columns
  --logic <formula>           Filter, a Logic Builder formula (see
                              /path-logic-formula-syntax); COLUMN("<computed
                              column>") and CUSTOMFIELD("<custom field>")
                              compare this report's own
  --include-archived          Include archived records
  --context <text>            The report's context: what it is for, who uses
                              it, how to read it — shown with it to people and
                              AI agents (at most 50,000 characters)
  --context-file <path>       Read the report's context from a text or markdown
                              file (instead of --context)
  --column-context <json>     Context on columns by name, e.g. '{"Margin":"Sell
                              minus cost, per job"}' (computed columns: say how
                              they are calculated)
  --roles <names>             Share with these roles (comma-separated)
  --role-permissions <perms>  Comma-separated: view,edit,delete (default view)
  --users <names>             Share with these users (comma-separated: names as
                              list-users shows them, or emails)
  --user-permissions <perms>  Comma-separated: view,edit,delete (default view)
  --data <json>               Escape hatch: the create body as JSON (keys:
                              name, info, report_type, scheduling, columns,
                              filter_condition, include_archived,
                              show_user_filter, user_filter_condition_keys);
                              flags override it
  --dry-run                   Build and validate the body and print it, but
                              create nothing
  --preview                   Run the report as built and print its rows, but
                              save nothing (no --name needed): try a report
                              before creating it, then create it with the same
                              options
  --groups-only               With --preview on a grouped report: only the
                              group total rows
  --page <n>                  With --preview: page to show (default: "1")
  --per-page <n>              With --preview: rows per page (default: "50")
  --all                       With --preview: every row in one page
```

## Examples

- args: `--bo-name Job --name "Won this month" --columns "Job Name,Estimated Sell Price" --logic 'ISTHISMONTH(STEPFIRSTOCCURRED("Job Won"))'`

## Notes

Creates a report the way the web's report builder does: one business object's records that meet a filter, with the columns chosen (fields, linked records' fields, milestone and step dates, counts, calculations), optionally grouped with totals. The full how-to (picking columns, writing context, previewing with the user) is the [`cli-reports-skill` workflow](../workflows/reports.md).

**Examples**

```
create-report --bo-name Job --preview --columns 'Job Outside Salesperson.Person Name | group | name=Salesperson, count | name=Jobs Won, Total Sell With Tax | total | name=Total Sell' --logic 'ISTHISMONTH(STEPFIRSTOCCURRED("Job Won"))' --groups-only
create-report --bo-name Job --name "Won Jobs This Month by Salesperson" --columns 'Job Outside Salesperson.Person Name | group | name=Salesperson, count | name=Jobs Won, Total Sell With Tax | total | name=Total Sell' --logic 'ISTHISMONTH(STEPFIRSTOCCURRED("Job Won"))' --context "Jobs first won this month, by outside salesperson." --roles "Sales Manager"
create-report --bo-name Job --name "Retail Jobs Margin" --columns 'Job Number, Job Name, milestone:Ordering | name=Ordered On' --column 'CALC([Total Sell of Job Items] - [Total Cost of Job Items]) | name=Margin | context=Sell minus cost of the job items' --logic '[Department] = "Retail" AND COLUMN("Margin") > 1000'
create-report --bo-name Job --name "Jobs Open Over 30 Days" --columns 'Job Number, Job Name, milestones' --custom-field 'TIMEBETWEEN(field:Created Date, today) | name=Days Open' --logic 'CUSTOMFIELD("Days Open") > DAYS(30) AND NOTINMILESTONE("Closed")'
```

- **Who may create:** Create Reports on the business object. The creator becomes the owner (view, edit, delete); `--roles` / `--users` share it. `--role-permissions` / `--user-permissions` take `view`, `edit`, `delete`, the same words as `create-folder`: every share grants view, `edit` and `delete` add to it. Every role and user is looked up before anything is created; one that matches nothing stops the command and no report is created.
- **Columns:** `--columns` is a comma-separated list, each `<source> | option | …`. Sources: `Field`, `Relationship.Field` (`[…]` around a name with a comma), `milestone:<name>` / `step:<name>` (entry date; `occurrence=first` by default), `milestones` (current milestones), `count`, `CALC(<expression>)`, `TIMEBETWEEN(<from>, <to>)` with ends `today`, `field:<date field>`, `milestone:<name>`, `step:<name>`, also on a linked record (`milestone:Order Related PO.Received`). A CALC with commas goes in its own `--column`.
- **Options:** `name=` (a CALC is otherwise named "Custom Calculation" and a TIMEBETWEEN "Time Between"; two of them can't share a name), `group` (`group=month`, `group=1000`, `group=1d`), `total|average|minimum|maximum` (numbers, currency, percent, time spans), `sort=asc|desc`, `context=`, formatting. At most 3 Group columns; a Group column makes the report grouped.
- **Filter:** `--logic` is a Logic Builder formula (the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md); [find-records-syntax.md](../find-records-syntax.md) in the [`path-cli` guide](../../../pathwd-guide/references/commands.md)); reports also take `?`, entry dates, `COLUMN("<computed column>")` and `CUSTOMFIELD("<Custom Field>")`. It is validated as the Logic Builder does before anything is saved. User filters are ticked after: `set-filter report <id> --user-filter`.
- **Custom Field** (`--custom-field`): a CALC or TIMEBETWEEN the filter compares but the report doesn't show.
- `--preview` builds and runs the report without saving (no `--name`; `--groups-only`, `--page`, `--all`; View on the business object is enough). A saved report's columns, grouping and totals can't be changed with these commands, so the user sees the preview first. `--dry-run` prints the create body.

**Output:** `Created report 7402 "Won by salesperson" on Jobs (bo 5050) — grouped, 3 columns`, then indented `columns:`, `filter:`, `owner:`, the shares and `link:`, and a `Check it: run-report --report-id 7402 …` line; `--quiet` prints the id. `--preview` prints `Preview — not saved: …` and the rows as `run-report` prints them (`record` and the columns, footer with rows and group totals; `--groups-only`: the group rows alone, `[5 groups of 82 records]`). `--dry-run` prints the create body without creating.

**Common errors**

- `<user> can't create a report on Jobs (business object 5050) — they don't have Create Reports permission on Jobs (business object 5050), as in the web app.`
- `Column "<spec>": …`: the column's problem, e.g. `total / average / minimum / maximum need a number, currency, percent or time span column (this one is text)`, `group=month groups dates: …`, an unknown field (spelling from `list-fields`).
- `Two custom columns / Custom Fields are named "Custom Calculation" — give one a different name=`.
- `A grouped report needs a Group column: add "| group" …` (`--type grouped` without one).
- `Role "…" not found — did you mean "…"? No report was created.` / `User "…" not found (session-context lists them). No report was created.`: nothing was created; the close names are roles and active users of the account.
- `The report's owner is always you, its creator: shares take view, edit, delete.` / `--user-permissions takes view, edit, delete (comma-separated), not …`
- `Name the report: --name <name>`; a server error ending `The combination of name and business object ID must be unique`.

**Related:** `run-report`, `get-report`, `update-report`, `set-filter report <id>`, `create-custom-field`, `find-records --group-by` (a one-off grouped answer, nothing saved), `create-folder`.

Related commands: [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
