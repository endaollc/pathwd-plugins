# delete-folder-sticker

Runs with: path_delete_folder_report_items(command: "delete-folder-sticker", args: "<flags>") (remove).

```
Usage: delete-folder-sticker [options]

Delete one of the folder's own stickers (permanent). Asks for confirmation
(--confirm <code>); --dry-run needs none. An inherited business object sticker
can't be deleted here: hide it on the folder with hide-folder-sticker. Needs
Edit on the folder

Options:
  --folder-id <id>        Folder ID
  --folder-name <name>    Folder name (requires --bo-id or --bo-name)
  --bo-id <id>            Business object ID (for --folder-name)
  --bo-name <name>        Business object name (for --folder-name)
  --sticker <id-or-name>  The folder sticker: its id (list-folders) or name; an
                          inherited one also by its business object sticker id
  --dry-run               Print the save body, but save nothing
```

## Examples

- args: `--folder-id 88 --sticker Overdue`

## Notes

Deletes one of the folder's own stickers, permanently. A sticker inherited from the business object isn't deleted here: `hide-folder-sticker` hides it on this folder, and deleting the business object sticker is Designer work.

**Examples**

```
delete-folder-sticker --folder-id 21110 --sticker Trade
delete-folder-sticker --folder-id 21110 --sticker Trade --confirm DEL-3F9A1C07
delete-folder-sticker --folder-name "All My Jobs (ISS)" --bo-name Job --sticker ISS --dry-run
```

- **Confirmation:** without `--confirm` nothing is deleted. The answer starts `NOT DELETED: this needs confirmation.`, names the sticker and folder (`folder sticker 3395 "Trade" on folder 21110 "In Process" (Services)`) and gives a code. The user confirms; then the same command again with `--confirm <code>` deletes it. The code (`DEL-` and 8 characters) works only for that exact command line and expires within 20 minutes. `--dry-run` needs no code.
- `--sticker` takes the folder sticker id (from `list-folders`) or its name.
- When the folder's stickers then match the business object's (no own stickers left, nothing hidden), it is back to Standard; otherwise it stays Custom.
- Needs Edit on the folder; a user without it is refused before any code is given.

**Output:** `Deleted sticker 3395 "Trade" from folder 21110 "In Process"`, and `… is back to Standard stickers.` when that happened.

**Common errors**

- `Sticker 2736 "High Priority" on folder 21110 is inherited from business object sticker 443: hide it on this folder with hide-folder-sticker (deleting the sticker itself is Designer work).`
- `No sticker "X" on folder 21110 "In Process". Stickers: …`; `Folder 21110 has more than one sticker named "X"; use its id.`
- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: run it again without `--confirm` for a new code (and confirm again).
- `<user> can't change the stickers of folder 21110 "In Process" — they don't have Edit permission on folder 21110, as in the web app.`

**Related:** `list-folders`, `hide-folder-sticker`, `update-folder-sticker`, `reset-folder-stickers`.

Related commands: [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md), [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md).
