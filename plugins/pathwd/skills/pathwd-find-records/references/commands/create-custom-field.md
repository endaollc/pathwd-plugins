# create-custom-field

Runs with: path_manage_folders_reports(command: "create-custom-field", args: "<flags>") (change).

```
Usage: create-custom-field [options] <report-id>

Add a filter-only Custom Field to a report: a calculation (--expression,
expression syntax) or a Time Between (--from/--to: today | field:<date field> |
milestone:<name> | step:<name>, or the same on a related record through to-one
relationships, e.g. milestone:Order Related PO.Received). Compare it in the
report filter with CUSTOMFIELD("name"); time spans compare in DAYS()/HOURS()...

Arguments:
  report-id                              Report ID

Options:
  --name <name>                          Custom Field name (unique among the report custom columns/fields)
  --expression <formula>                 Calculation, e.g. "[Total] - [Paid]"
  --from <source>                        Time Between start, e.g. milestone:Won or milestone:<Relationship>.<name> on a related record
  --to <source>                          Time Between end (same forms as --from)
  --from-occurrence <first|most_recent>  For milestone/step sources: first | most_recent (default most_recent)
  --to-occurrence <first|most_recent>    For milestone/step sources: first | most_recent (default most_recent)
  --context <text>                       The Custom Field's context: what it measures and why the filter compares it (at most 50,000 characters)
  --context-file <path>                  Read the context from a text or markdown file (instead of --context)
  --dry-run                              Print the column without saving
```

## Notes

Adds a Custom Field to a saved report: a calculation or a Time Between that the report's filter compares but the report doesn't show (the report builder's Custom Fields). Adding one doesn't change which records the report returns until a filter rule compares it.

**Examples**

```
create-custom-field 3830 --name "Days Open" --from "field:Created Date" --to today --context "Days since the job was created."
create-custom-field 3830 --name "Margin" --expression "[Total Sell of Job Items] - [Total Cost of Job Items]"
create-custom-field 2542 --name "Warehouse Days" --from "milestone:Fully Received" --to "milestone:Fully Delivered" --from-occurrence first
create-custom-field 6800 --name "PO Wait" --from "milestone:Order Related PO.Received" --to today --dry-run
```

- The report id is positional (`list-reports`, `get-report`); `--name` is required and must differ from the report's other computed columns and Custom Fields.
- **Calculation** (`--expression`): the calculation-field expression language, per record: `[Field]`, `[Relationship.Field]`, `+ - * /`, `IF(…)`, milestone/step entry dates; no group totals.
- **Time Between** (`--from` and `--to`, both needed): `today`, `field:<date field>`, `milestone:<name>`, `step:<name>`, or the same on a linked record through one-to-one relationships (`milestone:Order Related PO.Received`; no linked record = no value). Milestone/step ends use the most recent time unless `--from-occurrence` / `--to-occurrence first`.
- **Then compare it** in the filter: `set-filter report 3830 --text '<the current formula> AND CUSTOMFIELD("Days Open") > DAYS(30)'` (time spans compare with `DAYS()`, `HOURS()`…). `get-filter report <id>` gives the current formula. To show the value as well, make it a column instead (`create-report --column`).
- Needs Edit on the report.

**Output:** `Added Custom Field "Days Open" to report 3830 — compare it with CUSTOMFIELD("Days Open") (set-filter report 3830)`. `--dry-run` prints the column it would add, without saving. `get-report 3830` shows it under `custom fields:`.

**Common errors**

- `Report 3830 already has a custom column or field named "X".`
- `Time Between source "X" must be today, field:<date field>, milestone:<name> or step:<name> (on a related record: milestone:<Relationship>.<name>)`; `Field "X" is not a date field (…)`.
- `A Time Between Custom Field needs both --from and --to`; `Use --expression or --from/--to, not both`; `Provide --expression <formula> or --from/--to`.
- An expression parse error naming the unknown field or function.
- `<user> can't change report "Jobs Test Report" — they don't have Edit permission on report "Jobs Test Report", as in the web app.`

**Related:** `get-report` (Custom Fields, `[unused]` ones), `update-custom-field`, `delete-custom-field`, `set-filter report <id>`, `create-report --custom-field`.

Related commands: [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
