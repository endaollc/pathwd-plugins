# update-report

Runs with: path_manage_folders_reports(command: "update-report", args: "<flags>") (change).

```
Usage: update-report [options]

Change a report's context or settings, saved the way the report form saves it
(columns, filter, scheduling as stored). Needs Edit on the report. --data keys:
name, include_archived, info, column_info (filter: set-filter report; Custom
Fields: create-custom-field / update-custom-field)

Options:
  --report-id <id>       Report ID
  --report-name <name>   Report name (resolved to ID)
  --context <text>       Context: what it is for, who uses it, how to read it —
                         shown with it to people and AI agents ("" clears it;
                         at most 50,000 characters)
  --context-file <path>  Read the context from a text or markdown file (instead
                         of --context)
  --data <json>          JSON object of report keys to change, e.g.
                         '{"name":"Open
                         Quotes","context":"…","column_context":{"Total":"Quoted
                         price incl. tax"}}' — column_context sets each named
                         column's own context (by column id, column_key or
                         name; null clears it, and the column shows its field's
                         context again)
  --dry-run              Print the save body without saving
```

## Notes

Changes a report's name, context, column context or archived setting, saved the way the web's report form saves it: columns, filter and scheduling stay as stored. It can't change a saved report's columns, grouping or totals.

**Examples**

```
update-report --report-id 2542 --context "Average days from Fully Received to Fully Delivered per salesperson, for the warehouse review."
update-report --report-name "Jobs Test Report" --data '{"name":"Jobs Created - Last 30 Days","include_archived":true}'
update-report --report-id 2542 --data '{"column_context":{"Received to Delivered":"Time from the order entering Fully Received to entering Fully Delivered."}}'
update-report --report-id 2542 --context ""
```

- Name the report with `--report-id`, or `--report-name` (any business object; an ambiguous name lists the matches with ids).
- `--context` / `--context-file` set the report's context (`""` clears it; at most 50,000 characters).
- `--data` keys: `name`, `include_archived`, `context`, `column_context`. `column_context` is `{"<column id, column_key or name>": "text" | null}`; null clears it and the column shows its field's context again. A computed column's context says how it is calculated.
- Elsewhere: the filter and user filters `set-filter report <id>`; Custom Fields `create-custom-field`, `update-custom-field`, `delete-custom-field`.
- Columns, grouping and totals: the user changes them in the web app (the report's Edit), or a corrected report is made with `create-report` under a new name. `create-report --preview` before saving avoids this.
- Sharing is set when the report is created (`create-report --roles`); later changes are made in the web app.
- Needs Edit on the report (its owner has it; Admins and report admins: every report).

**Output:** `Updated report 2542 "Avg. Time in Warehouse - By Salesperson": context (96 characters)` (or the keys changed). `--dry-run` prints the save body without saving.

**Common errors**

- `<user> can't change report "…" — they don't have Edit permission on report "…", as in the web app.`
- `update-report doesn't change columns. --data keys: name, include_archived, info, column_info. Filter: set-filter report; …`
- `Report "X" not found`; `Ambiguous report name "X" — use --report-id. Matches: …`
- `No column "X" on report 2542. Columns: …` (a `column_context` key).
- A server error ending `The combination of name and business object ID must be unique`: the business object already has a report with that name.

**Related:** `get-report`, `set-filter report <id>`, `create-custom-field`, `create-report`, `update-folder`.

Related commands: [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
