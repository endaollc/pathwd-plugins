# show-folder-sticker

Runs with: path_manage_folders_reports(command: "show-folder-sticker", args: "<flags>") (change).

```
Usage: show-folder-sticker [options]

Show again an inherited business object sticker hidden on this folder. When
nothing on the folder then differs from the business object, the folder goes
back to Standard stickers. Needs Edit on the folder

Options:
  --folder-id <id>        Folder ID
  --folder-name <name>    Folder name (requires --bo-id or --bo-name)
  --bo-id <id>            Business object ID (for --folder-name)
  --bo-name <name>        Business object name (for --folder-name)
  --sticker <id-or-name>  The folder sticker: its id (list-folders) or name; an
                          inherited one also by its business object sticker id
  --dry-run               Print the save body, but save nothing
```

Related commands: [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
