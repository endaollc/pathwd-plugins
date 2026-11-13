# update-folder

Runs with: path_manage_folders_reports(command: "update-folder", args: "<flags>") (change).

```
Usage: update-folder [options]

Change a folder's context or settings, saved the way the folder form saves it
(everything else as stored). Needs Edit on the folder. --data keys:
folder_name, include_archived, columns, sorts, default_sort_field,
default_sort_direction, map_enabled, map_relationship_id, info, sticker_info
(criteria: set-filter folder; stickers: create-folder-sticker,
update-folder-sticker, delete-folder-sticker, hide-folder-sticker,
show-folder-sticker, reset-folder-stickers)

Options:
  --folder-id <id>       Folder ID
  --folder-name <name>   Folder name (requires --bo-id or --bo-name)
  --bo-id <id>           Business object ID (for --folder-name)
  --bo-name <name>       Business object name (for --folder-name)
  --context <text>       Context: what it is for, who uses it, how to read it —
                         shown with it to people and AI agents ("" clears it;
                         at most 50,000 characters)
  --context-file <path>  Read the context from a text or markdown file (instead
                         of --context)
  --data <json>          JSON object of folder keys to change, e.g.
                         '{"folder_name":"Active
                         Jobs","context":"…","sticker_context":{"Late":"Past
                         its due date"}}' — sticker_context sets each named
                         sticker's own context (by sticker id or name; null
                         clears it); columns (a list of {"field_id",
                         "custom_name", "percentage_width"}) makes the folder's
                         columns custom, null goes back to the default columns
  --dry-run              Print the save body without saving
```

## Notes

Changes a folder's name, context or settings, saved the way the web's folder form saves it: the folder is read fresh and everything not named stays as stored. Criteria and stickers have their own commands (below).

**Examples**

```
update-folder --folder-id 21110 --context "Services in progress, for the service managers' morning review."
update-folder --folder-name "Unpaid Jobs" --bo-name Job --data '{"folder_name":"Unpaid Invoices","include_archived":false}'
update-folder --folder-id 21110 --data '{"sticker_context":{"Trade":"A trade customer: pricing follows the trade price list."}}'
update-folder --folder-id 21110 --data '{"columns":[{"field_id":191628},{"field_id":191633,"custom_name":"Vendor","percentage_width":30}]}'
update-folder --folder-id 21110 --context ""
```

- Name the folder with `--folder-id`, or `--folder-name` plus `--bo-name` / `--bo-id`.
- `--context` / `--context-file` set the folder's context (`""` clears it; at most 50,000 characters); `context` in `--data` does the same.
- `--data` keys: `folder_name`, `include_archived`, `columns`, `sorts`, `default_sort_field`, `default_sort_direction`, `map_enabled`, `map_relationship_id`, `context`, `sticker_context`.
- `sticker_context`: `{"<sticker id or name>": "text" | null}`, each sticker's own context; only on a folder with Custom stickers (a Standard folder shows the business object stickers' context, which is Designer work).
- `columns` (`[{"field_id": 191628, "custom_name": "Job Name", "percentage_width": 25}, …]`, in display order) replaces the folder's columns and makes them custom, as the folder form's Custom columns switch does; on a folder with the business object's default columns it switches it to custom. `custom_name` defaults to the field's name, `percentage_width` to 0. `"columns": null` goes back to the default columns. A field id the folder can't show the user is refused as not found. `--dry-run` prints the body first.
- Elsewhere: criteria and user filters `set-filter folder <id>`; stickers `create-folder-sticker`, `update-folder-sticker`, `delete-folder-sticker`, `hide-folder-sticker`, `show-folder-sticker`, `reset-folder-stickers`. Access (who sees the folder) is changed in the web app.
- Needs Edit on the folder (Admins and folder admins: every folder).

**Output:** `Updated folder 21110 "In Process": context (78 characters)` (or the keys changed; columns read `columns (custom, 2)` or `columns (default)`). `--dry-run` prints the save body without saving.

**Common errors**

- `<user> can't change folder 21110 "In Process" — they don't have Edit permission on folder 21110, as in the web app.`
- `update-folder doesn't change filter_condition. --data keys: … Criteria: set-filter folder. …`: the key belongs to another command.
- `Nothing to change: pass --context <text>, --context-file <path> or --data <json> …`
- `columns takes a list of at least one column (…), or null for the default columns`; `columns[0] needs a field_id: …`; `columns[0]: field 191628 not found`.
- `Folder 21110 "In Process" has Standard stickers; their context is the business object sticker's (Designer CLI).`
- `Folder "X" not found on Jobs — did you mean "…"?`; `--folder-name requires --bo-id or --bo-name to identify the business object`.
- A child account's inherited folder: `Folder … is inherited from the parent account and can't be changed here.`
- A server error ending `Folder name is not unique`: another folder of the business object has that name.

**Related:** `list-folders`, `set-filter folder <id>`, `create-folder`, `update-report`.

Related commands: [`create-folder`](create-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
