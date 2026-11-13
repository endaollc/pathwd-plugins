# reset-folder-stickers

Runs with: path_delete_folder_report_items(command: "reset-folder-stickers", args: "<flags>") (remove).

```
Usage: reset-folder-stickers [options]

Put a folder's stickers back to Standard: only its business object's stickers,
all shown. Its own stickers are deleted and hidden ones show again, so when
that discards anything it asks for confirmation first (--confirm <code>), as
the folder form asks. Needs Edit on the folder

Options:
  --folder-id <id>      Folder ID
  --folder-name <name>  Folder name (requires --bo-id or --bo-name)
  --bo-id <id>          Business object ID (for --folder-name)
  --bo-name <name>      Business object name (for --folder-name)
  --dry-run             Print the save body, but save nothing
```

## Notes

Puts a folder's stickers back to Standard: only its business object's stickers, all shown. The folder's own stickers are deleted and the business object stickers hidden on it show again, as the web's folder form does when Standard is chosen.

**Examples**

```
reset-folder-stickers --folder-id 21110 --dry-run
reset-folder-stickers --folder-id 21110
reset-folder-stickers --folder-id 21110 --confirm DEL-8B20E4D1
```

- **Confirmation:** when the reset would delete own stickers or show hidden ones again, it first answers `NOT DELETED: this needs confirmation.`, lists them (`folder 21110 "In Process" (Services)'s own sticker "Trade" (3395) (reset-folder-stickers puts it back to Standard stickers)`) and gives a code. The user confirms; then the same command again with `--confirm <code>`. The code works only for that exact command line and expires within 20 minutes. A reset that discards nothing runs without a code.
- `--dry-run` prints the save body and a `NOTE: going back to Standard: …` line saying what would go; it needs no code.
- One own sticker is removed with `delete-folder-sticker`; one hidden sticker shown again with `show-folder-sticker`. The reset does both for all of them.
- The folder is named by `--folder-id`, or `--folder-name` with `--bo-name` / `--bo-id`. Needs Edit on the folder.

**Output:** `folder 21110 "In Process" is back to Standard stickers: its own stickers "Trade" are deleted`, or `… already has Standard stickers; nothing saved.`

**Common errors**

- `reset-folder-stickers no longer takes --yes: …`: confirmation is the `--confirm <code>` exchange above.
- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: run it again without `--confirm` for a new code.
- `<user> can't change the stickers of folder 21110 "In Process" — they don't have Edit permission on folder 21110, as in the web app.`

**Related:** `list-folders` (`[Standard]` / `[Custom]` and each sticker), `delete-folder-sticker`, `show-folder-sticker`, `hide-folder-sticker`, `create-folder-sticker`.

Related commands: [`delete-folder-sticker`](delete-folder-sticker.md), [`delete-custom-field`](delete-custom-field.md), [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md).
