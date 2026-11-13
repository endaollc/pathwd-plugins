---
name: pathwd-find-records
description: "PATHwd searching and filtering: the Logic Builder formula syntax ([Field] = \"value\", AND/OR, ISANYOF, CONTAINS, INTHELAST, INMILESTONE, CURRENTUSER(), SETTING(), BLANK), advanced searches with find-records, and creating or changing folders, folder stickers and reports with their filters, columns, grouping and access. Covers requests like \"which jobs are waiting on a deposit?\" or \"make a folder of won residential jobs\"."
---

# Finding records, folders and reports in PATHwd

PATH describes which records belong somewhere with Logic Builder formulas: folder and report filters, folder stickers, milestone criteria, task rules, Fork steps and visibility rules all use the same syntax. On the PATHwd connector, a formula runs as an advanced search (`find-records`, nothing saved) or is saved as a folder's or report's filter; formulas of every other target can be read and checked (`get-filter`, `parse-filter`).

A folder is a saved list of one business object's records that meet its filter, shared with chosen users and roles. A report is a table of one business object's records with the columns the user picks, optionally grouped with totals.

The account, session and the connector's tools are described in the **pathwd-guide** skill.

## Guides

- [path-logic-formula-syntax](references/logic-formula-syntax.md): Use to read, write, validate or save a PATH Logic Builder formula in the user app.

Step-by-step workflows:

- [find-records (workflow)](references/workflows/find-records.md): Use when the user wants to find records matching conditions they describe in plain language — an advanced search, run now, nothing saved.
- [create-folder (workflow)](references/workflows/folders.md): Use when the user wants to create a folder in PATH.
- [logic-builder (workflow)](references/workflows/logic-builder.md): Use when the user describes rules for which records a folder or report should show.
- [create-report (workflow)](references/workflows/reports.md): Use when the user wants a new report in PATH, or to fix one just made.

Reference files:

- [find-records: formula syntax, recipes and common mistakes](references/find-records-syntax.md): `find-records` is PATH's advanced search: every record of one business object that matches a Logic Builder formula, run on the spot, nothing saved.

## Commands

| Command | Tool | What it does |
|---------|------|--------------|
| [`create-custom-field`](references/commands/create-custom-field.md) | `path_manage_folders_reports` | Add a filter-only Custom Field to a report: a calculation (--expression, expression syntax) or a Time Between (--from/--to: today \| field:<date field>… |
| [`create-folder`](references/commands/create-folder.md) | `path_manage_folders_reports` | Create a folder with permissions (and its context: --context / --context-file) |
| [`create-folder-sticker`](references/commands/create-folder-sticker.md) | `path_manage_folders_reports` | Add a sticker of the folder's own (shown on its rows when its formula matches), saved the way the folder form saves it |
| [`create-report`](references/commands/create-report.md) | `path_manage_folders_reports` | Create a report the way the web report builder does: columns (fields, Relationship.Field, milestone/step dates, Milestones, Count, CALC(<expression>)… |
| [`delete-custom-field`](references/commands/delete-custom-field.md) | `path_delete_folder_report_items` | Remove a report Custom Field |
| [`delete-folder-sticker`](references/commands/delete-folder-sticker.md) | `path_delete_folder_report_items` | Delete one of the folder's own stickers (permanent) |
| [`find-records`](references/commands/find-records.md) | `path_find_records` | Advanced search: the records of one business object that match a Logic Builder formula (e.g. 'ISLASTWEEK([Created Date]) AND HASSTEP("Job Won")'), run… |
| [`get-filter`](references/commands/get-filter.md) | `path_data_model` | Print a stored Logic Builder filter as formula text (milestone criteria, task criteria, Fork conditions, folder/report/sticker filters, visibility… |
| [`get-report`](references/commands/get-report.md) | `path_folders_reports` | Show a report: its business object and type, its columns (column id, name, type, hidden, the COLUMN("name") a filter compares, a computed column's… |
| [`hide-folder-sticker`](references/commands/hide-folder-sticker.md) | `path_manage_folders_reports` | Hide an inherited business object sticker on this folder (it keeps showing on the business object's other folders) |
| [`list-folders`](references/commands/list-folders.md) | `path_folders_reports` | List the folders in the user's folder menu: id, business object, name |
| [`list-reports`](references/commands/list-reports.md) | `path_folders_reports` | List the reports the user can open: id, business object, grouped, name |
| [`parse-filter`](references/commands/parse-filter.md) | `path_data_model` | Validate a Logic Builder formula (e.g. '[Department] = "Retail" AND INMILESTONE("Lead")') against a business object without saving it |
| [`reset-folder-stickers`](references/commands/reset-folder-stickers.md) | `path_delete_folder_report_items` | Put a folder's stickers back to Standard: only its business object's stickers, all shown |
| [`run-report`](references/commands/run-report.md) | `path_folders_reports` | Run a report and print its rows (the web app's report view; in real-world mode only reports the acting user can open): a table of record (the record… |
| [`set-filter`](references/commands/set-filter.md) | `path_manage_folders_reports` | Replace a stored Logic Builder filter from formula text |
| [`show-folder-sticker`](references/commands/show-folder-sticker.md) | `path_manage_folders_reports` | Show again an inherited business object sticker hidden on this folder |
| [`update-custom-field`](references/commands/update-custom-field.md) | `path_manage_folders_reports` | Change a report Custom Field's name, definition (--expression, or --from/--to) or context (--context / --context-file) |
| [`update-folder`](references/commands/update-folder.md) | `path_manage_folders_reports` | Change a folder's context or settings, saved the way the folder form saves it (everything else as stored) |
| [`update-folder-sticker`](references/commands/update-folder-sticker.md) | `path_manage_folders_reports` | Change one of the folder's own stickers: name, icon, color, formula and/or context (the rest as stored) |
| [`update-report`](references/commands/update-report.md) | `path_manage_folders_reports` | Change a report's context or settings, saved the way the report form saves it (columns, filter, scheduling as stored) |
