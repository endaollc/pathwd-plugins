# update-record-note

Runs with: path_update_record(command: "update-record-note", args: "<flags>") (change).

```
Usage: update-record-note [options]

Change the text of a note in a record's Notes field, as its Edit Note form
does. Only the note's author can (list-record-notes shows which are yours)

Options:
  --record-id <id>    Record ID
  --note-id <id>      Note id (from list-record-notes)
  --note <text>       The new note text
  --note-file <path>  Read the new note text from a file (instead of --note)
```

## Notes

Replaces the text of a note in a record's Notes field, as the note's Edit Note form does. Only the note's author can edit it (`mine: true` in `list-record-notes`); the note keeps its author and date and shows as edited.

**Examples**

```
update-record-note --record-id 6adba16ca29a6293f946ca4f --note-id 6adf6815ca742e5ee6784809 --note "Deposit check received 2026-11-10; cleared 2026-11-12."
update-record-note --record-id 6adba16ca29a6293f946ca4f --note-id 6adf60eeca742e5ee6783005 --note-file revised-note.txt
```

- `--note-id` is the note's `id` from `list-record-notes` (24 hex).
- The new text replaces the whole note: to add to it, read the current text first and send the combined text. `--note-file` reads it from a file.

On PATH WD Remote MCP `--note-file` reads a workspace file; `write_workspace_file` writes one.

**Output:** one line, `Updated note 6adf6815ca742e5ee6784809 in Job Notes on Job 26-0073 (6adba16ca29a6293f946ca4f)`. `--quiet`: the note id.

**Common errors**

- `Note X isn't on record Y. List them with list-record-notes --record-id Y`: a wrong note id, or the note is on another record.
- `<user> can't edit note … on record … — only the note's author can edit or delete it, as in the web app.`: ask the author, or add a new note instead.
- `<user> can't edit note … — the "X" (id) Notes field isn't visible to them in the record viewer, as in the web app.`
- `Give the note text with --note <text> or --note-file <path>` / `The note is empty`.

**Related:** `list-record-notes`, `add-record-note`, `delete-record-note`.

Related commands: [`update-record`](update-record.md), [`unarchive-record`](unarchive-record.md), [`unarchive-record-file`](unarchive-record-file.md), [`set-appointment`](set-appointment.md), [`update-record-file`](update-record-file.md), [`update-message`](update-message.md), [`list-record-notes`](list-record-notes.md).
