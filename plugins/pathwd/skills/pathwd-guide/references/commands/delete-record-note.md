# delete-record-note

Runs with: path_remove_from_record(command: "delete-record-note", args: "<flags>") (remove).

```
Usage: delete-record-note [options]

Delete a note from a record's Notes field, as the Delete button of its Edit
Note form does. Only the note's author can (list-record-notes shows which are
yours). Asks for confirmation (--confirm <code>)

Options:
  --record-id <id>  Record ID
  --note-id <id>    Note id (from list-record-notes)
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --note-id 6adf6815ca742e5ee6784809`

## Notes

Deletes a note from a record's Notes field for good, as the Delete button of the note's Edit Note form does. Only the note's author can delete it (`mine: true` in `list-record-notes`). It can't be undone.

**Examples**

```
delete-record-note --record-id 6adba16ca29a6293f946ca4f --note-id 6adf6815ca742e5ee6784809
delete-record-note --record-id 6adba16ca29a6293f946ca4f --note-id 6adf6815ca742e5ee6784809 --confirm DEL-8B2E40D1
```

- `--note-id` is the note's `id` from `list-record-notes` (24 hex).
- **Two calls.** The first deletes nothing: it exits with code 3 and says what it would delete, with a confirmation code:

  ```
  NOT DELETED: this needs confirmation.
  It would permanently delete note 6adf6815ca742e5ee6784809 by Pat Lee on Oct 26, 2026 at 9:47 AM in Bad Debt Written Off Notes on Job 26-1854: QB Test Job B 2026-10-23 (6adba16ca29a6293f946ca4f): "2nd note here" in account 1668.
  ```

  Show that to the user; once they say yes, run the same command again with `--confirm <code>`. The code works only for this exact command and arguments, and expires within 20 minutes.
- To correct a note rather than remove it, `update-record-note`.

**Output:** one line, `Deleted note 6adf6815ca742e5ee6784809 from Job Notes on Job 26-0073 (6adba16ca29a6293f946ca4f)`. `--quiet`: the note id.

**Common errors**

- `Note X isn't on record Y. List them with list-record-notes --record-id Y`.
- `<user> can't delete note … on record … — only the note's author can edit or delete it, as in the web app.`: this comes before any confirmation code; only the author can remove it.
- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: run it without `--confirm` for a new code.

**Related:** `list-record-notes`, `update-record-note`, `add-record-note`.

Related commands: [`archive-record-file`](archive-record-file.md), [`delete-record-file`](delete-record-file.md), [`delete-message`](delete-message.md), [`add-record-note`](add-record-note.md), [`upload-record-file`](upload-record-file.md), [`send-message`](send-message.md), [`list-record-notes`](list-record-notes.md).
