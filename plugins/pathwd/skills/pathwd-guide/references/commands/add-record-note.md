# add-record-note

Runs with: path_add_to_record(command: "add-record-note", args: "<flags>") (add).

```
Usage: add-record-note [options]

Add a note to a record's Notes field, as the field's Add Note button does
(posted as you, now). Needs Edit on the business object and the field visible
to you in the record viewer

Options:
  --record-id <id>    Record ID
  --field <id|name>   The Notes field (optional when the record has only one)
  --note <text>       The note text
  --note-file <path>  Read the note text from a file (instead of --note)
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --note "Called the customer; delivery moved to Friday."`

## Notes

Adds a note to one of a record's Notes fields, as the field's Add Note button does: posted as the acting user, dated now. It shows in the record's History as Note Added. For a conversation with teammates (with @mentions and notifications) use `send-message`; to email someone, `send-record-email`.

**Examples**

```
add-record-note --record-id 6adba16ca29a6293f946ca4f --note "Called the homeowner; install moved to 2026-11-20."
add-record-note --record-id 6adba16ca29a6293f946ca4f --field "Job Notes" --note "Deposit check received at the office."
add-record-note --record-id 6adba16ca29a6293f946ca4f --field 191656 --note-file scope-notes.txt
```

- `--field` (id or name) is needed when the record has more than one Notes field the user can see; `list-record-notes --record-id <id>` lists them (and `get-record` names them under `notes`).
- The text is plain text and line breaks are kept; `--note-file` reads it from a file (for long text). Give one of `--note` / `--note-file`.
- The note is posted as the acting user (the signed-in user, or the one given with `--as-user`) and dated now; it can't be backdated.
- Needs Edit on the record's business object, and the field shown to the user in the record viewer.

On PATH WD Remote MCP `--note-file` reads a workspace file; `write_workspace_file` writes one.

**Output:** one line, `Added note 6adf6815ca742e5ee6784809 to Job Notes on Job 26-0073 (6adba16ca29a6293f946ca4f)`. `--quiet`: the note id.

**Common errors**

- `This record has 2 Notes fields — give --field <id|name>. Notes fields: 191656 Job Notes, 191681 Bad Debt Written Off Notes`: name the field.
- `This record has no Notes field you can see.`
- `X is not a Notes field. …` / `X shows a linked record's notes — add them on that record.`
- `Give the note text with --note <text> or --note-file <path>` / `Give --note or --note-file, not both` / `The note is empty`.
- `<user> can't add a note to Job Notes on record … — they don't have Edit permission on <business object>, as in the web app.` (or the field isn't visible to them in the record viewer): they can't add it in the web app either.
- Notes fields can't be set with `update-record`; it refuses them and names this command.

**Related:** `list-record-notes`, `update-record-note`, `delete-record-note`, `send-message`.

Related commands: [`upload-record-file`](upload-record-file.md), [`send-message`](send-message.md), [`archive-record-file`](archive-record-file.md), [`delete-record-file`](delete-record-file.md), [`delete-record-note`](delete-record-note.md), [`delete-message`](delete-message.md).
