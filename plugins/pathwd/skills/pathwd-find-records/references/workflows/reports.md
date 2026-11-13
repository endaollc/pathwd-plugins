# Workflow: create-report

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../../../pathwd-guide/references/commands.md) gives any command's syntax and the tool that runs it.

**Covers:** making a report: a table of one business object's records that meet set rules, with chosen columns (fields, linked records' fields, milestone and step dates, counts, calculations), optionally grouped with totals, averages, lowest or highest values per group. Commands: `create-report` (with `--preview`), `update-report`, `set-filter report`, the Custom Field commands, `run-report`. Each command's syntax and output: its command note.

The account and session: the [`path-session` guide](../../../pathwd-guide/references/session.md). Every command's syntax: the [`path-cli` guide](../../../pathwd-guide/references/commands.md).

**Access.** The CLI only does what the signed-in user could do in the web app. Creating a report needs **Create Reports** on its business object (the CLI refuses otherwise, naming the permission). The creator becomes the report's **owner** (full access) automatically, exactly as in the web app; you can also share it with roles and users. Changing it later needs Edit on that report.

## What to ask the user

**Required (ask for what's missing before creating anything):**
1. **What kind of records** — the business object (Jobs, Quotes, Services…)
2. **What to show** — the columns: which fields, which linked records' fields, totals or counts, calculated values
3. **Which records** — the rules (e.g. "won this month", "open", "Residential only"). "All of them" is a valid answer.

**Ask, but offer a default:**
- **Report name** — suggest one from the request ("Won Jobs This Month by Salesperson") and use it unless they want another.
- **Grouping and totals** — if they said "by salesperson" / "per month" / "total", that's a grouped report (see below). Otherwise a plain list.

**Optional (don't ask unless it matters):**
- **Who else can see it** — the user owns it either way. Share only if they name people or roles (default share level: view only).
- **Include archived records** — default no.
- **Sort order, number formats** — sensible defaults; number and date columns take their field's formatting.

## Steps

### Step 1: Load the business object (always, before writing the filter or running create-report)

Field names and types and the designers' context decide which fields the filter and columns should use, so load them first:

```
# Find the BO
--account <ID> list-business-objects

# The BO itself, with its context
--account <ID> get-business-object --bo-name <name>

# Its fields, with each field's type and context
--account <ID> list-fields --bo-name <name>

# Its relationships (linked records), with their context
--account <ID> list-relationships --bo-name <name>

# The fields of every related BO a filter leaf or column reaches through a relationship
--account <ID> list-fields --bo-id <related BO id>

# Milestones / steps, if the request mentions them (e.g. "has been won", "delivery scheduled")
--account <ID> list-workflow-milestones --bo-name <name>
--account <ID> list-workflow-steps --bo-name <name>

# Existing reports (avoid a duplicate; names are unique per business object)
--account <ID> list-reports
```

Skip what you already loaded this session. If your context no longer has it (for example after compaction), run it again with the global `--with-context` option (`--account <ID> --with-context list-fields …`): context is shown once per session, so a plain re-run leaves it out.

Columns to read: `list-fields` gives `name`, `type` (which column options a field takes: totals only on numbers, currency, percents and time spans) and `options` (a Selection's exact option texts; a list cut with `…` is printed whole by `get-field --field-id <id>`), with `Field context:` under the table; `list-relationships` gives `name` (the path in front of a linked column, `Job Main Customer.Customer Name`) and `to` (the linked business object and its id, for `--bo-id` above), with `Relationship context:`; the milestone and step lists give `name`, with their context under each table.

Map each of the user's words to a real field: "salesperson" might be a field on the record or a linked person (`Job Outside Salesperson.Person Name`); "sell" might be `Total Sell Price`. Read the context: it says what each field is for and often settles which one the user means. If two fields could fit, ask. If an existing report already does this, say so instead of making a duplicate.

### Step 2: Choose the report type and the columns

- **Detail report**: one row per record. The default.
- **Grouped report**: rows grouped by up to **3 Group columns** (e.g. Salesperson, then Month), with per-group values for Calculation columns (Total, Average, Minimum, Maximum) and Count. You get one as soon as a column is marked `group`; `--type grouped` / `--type detail` only double-checks it.

`--columns` takes the columns in order, separated by commas. Each column is a source, then `|`-separated options:

| Source | Column |
|---|---|
| `Sell Price` | A field on the report's business object |
| `Customer.Name` | A field of a linked record (relationship, then field; chains like `Job.Customer.Name` work). Put a name containing a comma in brackets: `[Last, First]` |
| `milestone:Won`, `step:Send Quote` | The date the record entered that milestone / had that step (`Customer.milestone:Active` for a linked record) |
| `milestones` | The record's current milestones (`Customer.milestones` for a linked record's) |
| `count` | The number of records (per group in a grouped report) |
| `CALC(<expression>)` | A Custom Calculation, e.g. `CALC([Sell Price] - [Cost])` — the expression language of calculation fields (`[Field]`, `[Relationship.Field]`, `+ - * /`, `IF(…)`, milestone/step entry dates) |
| `TIMEBETWEEN(<from>, <to>)` | The time between two dates; each end is `today`, `field:<date field>`, `milestone:<name>` or `step:<name>` — on the report's record, or on a linked record with the relationship path in front of the name, like a linked column: `milestone:Order Related PO.Received`, `field:Customer.Customer Since`, `step:Order Related PO.PO Job.Ship` (`Order Related PO.milestone:Received` works too). Linked ends follow one-to-one relationships (multi-hop) and read the first linked record; no linked record = an empty value |

| Option | Meaning |
|---|---|
| `name=<title>` | The column title (default: the field's name; `Custom Calculation`, `Time Between`, `Count`, `Milestones` for the special columns — give calculated columns a real name) |
| `context=<text>` | The column's context (see Step 5) |
| `group` | A Group column. Dates: `group=day\|week\|month\|quarter\|year`; numbers: `group=<range size>` (e.g. `group=1000`); time spans: `group=1d`, `group=12h`, `group=1d 4:30` |
| `total`, `average`, `minimum`, `maximum` | A Calculation column — number, currency, percent and time span columns only |
| `sort=asc` / `sort=desc` | Sort by this column |
| `occurrence=first\|most_recent` | Milestone / step columns: the first or latest time (default first) |
| `from-occurrence=…`, `to-occurrence=…` | The same for a TIMEBETWEEN's milestone / step ends (default most_recent) |
| `group-expression=<expression>` | CALC only: what its group rows show, with `TOTAL()`, `AVERAGE()`, `MINIMUM()`, `MAXIMUM()`, e.g. `group-expression=TOTAL([Sell Price]) - TOTAL([Cost])` |
| `currency[=<symbol>]`, `decimals=<n>`, `commas`, `percent` | Number formatting. Without these (or `format=`) a number or date field's column takes the field's own formatting — only set them to differ from it |
| `format=<date format>` | Date format, e.g. `M/d/yyyy` |
| `duration=days+hours` | Which parts a time span shows |
| `width=<n>` | Column width |

A CALC whose expression has commas goes in its own `--column '<spec>'` flag (repeatable), so its commas aren't read as column separators. The CLI refuses options the web app doesn't offer for a column (e.g. `total` on a text field, `group=month` on a number) and names the column.

**Worked example** — "won jobs this month by salesperson with total sell":

```
--account <ID> create-report --bo-name Jobs --name "Won Jobs This Month by Salesperson" \
  --columns 'Job Outside Salesperson.Person Name | group | name=Salesperson | sort=asc, count | name=Jobs Won, Total Sell Price | total | currency | decimals=2 | name=Total Sell' \
  --logic 'ISTHISMONTH(STEPFIRSTOCCURRED("Job Won"))' \
  --context "Jobs that first entered Won this calendar month, grouped by outside salesperson, with the number of jobs and their total sell price. For the monthly sales review." \
  --column-context '{"Total Sell":"Sum of Total Sell Price for the jobs in each salesperson group.","Jobs Won":"Number of jobs in the group."}' \
  --dry-run
```

### Step 3: Write the filter

`--logic '<formula>'` is a Logic Builder formula — load the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md) and follow it rather than guessing. There is no `NOT(...)`: negate a rule with its own negative form (`NOTHASSTEP`, `NOTINMILESTONE`, `ISNOTANYOF`, `NOTCONTAINS`, `!=`; the full table is in the syntax skill). When unsure, check the formula with `parse-filter --report` before creating. Report filters can also use:
- milestone / step entry dates: `INTHELAST(MILESTONEFIRSTENTERED("Quoting"), DAYS(30))`, `ISTHISMONTH(STEPLASTOCCURRED("Full Quote Sent to Customer"))`
- `?` for a value the person running the report fills in
- the report's own calculated columns: `COLUMN("Margin") > 1000` (a CALC or TIMEBETWEEN column), and its Custom Fields: `CUSTOMFIELD("Days Open") > DAYS(30)` (time spans compare with `DAYS()`, `HOURS()`…)

The CLI parses the formula and validates it exactly as the Logic Builder does before anything is saved; `--dry-run` shows the parsed filter (`filter: …`).

### Step 4: Add Custom Fields when the filter needs a value the report doesn't show

A **Custom Field** is a calculation or time-between the filter compares but the report never shows. Use one when the user wants records filtered on something computed ("open more than 30 days", "margin under 20%") without a column for it:

```
--custom-field 'TIMEBETWEEN(field:Created Date, today) | name=Days Open | context=Days since the ticket was created' \
--logic 'CUSTOMFIELD("Days Open") > DAYS(30)'
```

If they do want to see the value, make it a column (`--column 'TIMEBETWEEN(…) | name=Days Open'`) and compare it with `COLUMN("Days Open")` instead. Calculated columns and Custom Fields need different names.

### Step 5: Write the report's context and its columns' context

People and AI agents see this context every time they open or run the report, so write it for them:
- **Report context** (`--context` or `--context-file`): one to three sentences — what the report shows, which records are in it (the filter in plain words), who uses it and when. Use what the user told you; don't invent a purpose.
- **Column context** (`context=` on a column, or `--column-context '{"<column name>":"…"}'`): **every calculated column needs context that says how it is worked out** — "Sell Price minus Cost, per job", "Days from the job's first Won date to today", "Sum of Total Sell Price in each salesperson group". A field column already shows its field's context; give it its own only when the report uses the field in a special way.

### Step 6: Decide who can see it

The user owns the report (owner + view + edit + delete) automatically. To share:

```
--roles "Sales Manager" --role-permissions "view"
--users "Jane Smith,Bob Lee" --user-permissions "view,edit"
```

Levels: `view` (default), `view,edit`, `view,edit,delete`. For "everyone", look up the all-users role with `list-roles`.

### Step 7: Preview it with the user, then create

**Show the user the real rows before saving anything.** Run the same `create-report` command with `--preview` in place of `--name` and the sharing options: it builds exactly the report it would save (same columns, filter, grouping and totals), runs it on the server and prints its rows — nothing is saved. A grouped report shows each group's total row followed by its records; `--groups-only` shows just the group totals, `--page` / `--per-page` / `--all` page through the rows.

Walk the user through what it shows: the groups, the totals, a few records. Adjust the columns, grouping or filter and preview again until they have what they need — changing columns is free now, but not after saving (see Step 8). Only then save it.

Summarize in plain language before creating:

> "Here's the report I'll create on Jobs:
> **Name:** Won Jobs This Month by Salesperson
> **Shows:** one row per salesperson — how many jobs they won and the total sell
> **Includes:** jobs that first entered Won this month
> **Who sees it:** you (owner)
> OK?"

Then run the command with `--name` and the sharing options (`--dry-run` prints the exact body, creating nothing, if you want to check it). It prints the new report's id, columns, filter, owner, shares and a web link.

### Step 8: Run it and check

```
--account <ID> run-report --report-id <id>
--account <ID> get-report <id>
```

`run-report` prints the rows as a table: a `record` column (the record id `get-record` takes; `total` on a group's total row), then the report's columns by name as the web shows them, a footer counting rows and group totals (`[2 of 11 rows + 1 group total — --page 2 for more]`), and the report's and columns' context under it. Check the rows make sense: the right records (spot-check one with `get-record`), groups as asked, totals plausible. `get-report` shows each column, its context, the Custom Fields and the filter as formula text.

**Fixing it:**
| Problem | Fix |
|---|---|
| Wrong records | `set-filter report <id> --text '<formula>'` (`--dry-run` first) |
| Name, context, include archived | `update-report --report-id <id> --data '{"name":"…","include_archived":true}'`, `--context "<text>"` |
| A column's context | `update-report --report-id <id> --data '{"column_context":{"<column name>":"…"}}'` |
| A Custom Field | `create-custom-field <id> …`, `update-custom-field <id> <name> …`, `delete-custom-field <id> <name>` (asks for confirmation: show the user what it prints, then `--confirm <code>` only after their yes) |
| Wrong columns, grouping or totals | The CLI can't change a saved report's columns. Tell the user and either let them adjust it in the web app (Reports → the report → Edit), or, with their OK, create a corrected one under a new name and ask them to delete the old one in the web app. Previewing (`--preview`) until the columns are right avoids this. |

### Step 9: Report back

> "Done — I created **Won Jobs This Month by Salesperson** on Jobs (link). It groups this month's won jobs by outside salesperson with the count and total sell; right now it shows 4 salespeople, $182,400 in total. You own it. Want me to share it with anyone?"

## CLI Commands Used

| Command | Purpose |
|---------|---------|
| `list-business-objects`, `get-business-object`, `list-fields`, `list-relationships`, `list-workflow-milestones`, `list-workflow-steps` | Load the business object, its fields (types and context), linked records (and their fields), milestones and steps — before writing the filter and columns |
| `list-reports` | Existing reports (avoid duplicates; names are unique per business object) |
| `parse-filter --bo-name <n> --text '<formula>' --report` | Check a formula on its own |
| `list-roles`, `list-users` | Who to share with (`--users` takes the names `list-users` shows, or emails) |
| `create-report --preview` | Run the report as built and show its rows, saving nothing (try it with the user first) |
| `create-report` | Create the report (`--dry-run` prints the body) |
| `run-report --report-id <id>` | Check its rows |
| `get-report <id>` | Columns, Custom Fields, filter and context |
| `set-filter report <id>`, `update-report`, `create-custom-field` / `update-custom-field` / `delete-custom-field` | Fix it |
| `web-link report <id>` | The link to it in the web app |

`create-report --data '<json>'` takes a whole create body (columns, filter, scheduling…) for anything the flags can't express; flags given with it override it.

## Key facts and confirmations

- Every command here runs through the PATH WD Remote MCP tool for its area (each command's page in [the command index](../../../pathwd-guide/references/commands.md) names it).
- Names (fields, relationships, milestones, steps, roles, people) resolve through the commands; ids aren't guessed.
- `create-report --preview` runs the report as built without saving it: the user sees the rows, adjusts, and agrees before `create-report` saves it.
- A report carries a short context saying what it is for, and each calculated column context saying how it is worked out.
- Users know "grouped by salesperson", "the total sell per group", "who can see it", not `report_column_type` or `column_key`.
- A rule on a Selection field compares one of its options exactly (the `options` column of `list-fields`; `get-field` lists every one); when the user's word doesn't match, they pick the option.
- After creating, `run-report` shows what the report returns, and the user hears what it shows.
- A request that could mean two different reports is settled with the user first: a wrong report costs more than a question.
