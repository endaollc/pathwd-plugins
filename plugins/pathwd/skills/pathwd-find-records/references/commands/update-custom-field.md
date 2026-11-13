# update-custom-field

Runs with: path_manage_folders_reports(command: "update-custom-field", args: "<flags>") (change).

```
Usage: update-custom-field [options] <report-id> <field>

Change a report Custom Field's name, definition (--expression, or --from/--to)
or context (--context / --context-file). Filter rules keep working: they
reference the field's key, not its name.

Arguments:
  report-id                              Report ID
  field                                  Custom Field name or column_key

Options:
  --name <name>                          New name
  --expression <formula>                 New calculation
  --from <source>                        New Time Between start
  --to <source>                          New Time Between end
  --from-occurrence <first|most_recent>  For milestone/step sources: first | most_recent (default most_recent)
  --to-occurrence <first|most_recent>    For milestone/step sources: first | most_recent (default most_recent)
  --context <text>                       New context for the Custom Field ("" clears it)
  --context-file <path>                  Read the new context from a text or markdown file (instead of --context)
```

## Notes

Changes a report Custom Field (a filter-only calculation or Time Between): its name, its definition or its context. Filter rules that compare it keep working, renamed or not: they point at the field's key, not its name.

**Examples**

```
update-custom-field 3830 "ship time" --name "Days to Ship"
update-custom-field 3830 "Days to Ship" --from "field:Created Date" --to "field:Est Ship Date" --context "Days from the job's creation to its estimated ship date."
update-custom-field 3830 Margin --expression "[Total Sell of Job Items] - [Total Cost of Job Items] - [Change Order Cost Totals]"
update-custom-field 3830 82b5248e-7cb5-45cf-aaaa-a9d39278d1a1 --context ""
```

- Arguments: the report id, then the Custom Field's name (any case) or its `column_key`, both from `get-report <id>`.
- `--expression` or `--from` / `--to` replace the whole definition (a Time Between needs both ends again; `--from-occurrence` / `--to-occurrence first|most_recent`, default `most_recent`). The forms are those of `create-custom-field`.
- A new `--name` must differ from the report's other computed columns and Custom Fields; the filter then shows `CUSTOMFIELD("<new name>")`.
- `--context ""` clears the context.
- Only Custom Fields: a shown column's context is set with `update-report --data` (`column_context`); its definition is changed in the web app (the report's Edit).
- Needs Edit on the report.

**Output:** `Updated Custom Field "Days to Ship" on report 3830`.

**Common errors**

- `No Custom Field "X" on report 3830. Custom Fields: "ship time"`: a typo, or a shown column (which isn't a Custom Field).
- `Nothing to change: pass --name, --expression, --from/--to or --context`.
- `Report 3830 already has a custom column or field named "X".`
- `A Time Between Custom Field needs both --from and --to`; `Use --expression or --from/--to, not both`; a Time Between source or expression error as in `create-custom-field`.
- `<user> can't change report "Jobs Test Report" — they don't have Edit permission on report "Jobs Test Report", as in the web app.`

**Related:** `get-report`, `create-custom-field`, `delete-custom-field`, `set-filter report <id>`, `update-report`.

Related commands: [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
