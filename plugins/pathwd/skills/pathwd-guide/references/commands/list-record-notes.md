# list-record-notes

Runs with: path_record_activity(command: "list-record-notes", args: "<flags>") (read-only).

```
Usage: list-record-notes [options]

List a record's Notes fields and every note in them, newest first, as the
record viewer shows them: author, when (your clock), note id, and whether each
is yours (only the author can edit or delete a note). Only Notes fields the
record viewer shows you

Options:
  --record-id <id>   Record ID
  --field <id|name>  Only this Notes field
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084`

## Notes

A record's Notes fields and every note in them, newest first, as the record viewer shows them: author, when (on the user's clock), the note id, and whether the note is the user's own. A Notes field is a field on the record (such as "Job Notes") that holds dated notes; it isn't the record's Messages (`list-record-messages`) or its Emails (`list-record-emails`).

**Examples**

```
list-record-notes --record-id 6adba16ca29a6293f946ca4f
list-record-notes --record-id 6adba16ca29a6293f946ca4f --field "Job Notes"
list-record-notes --record-id 6adba16ca29a6293f946ca4f --field 191681
```

- `--field` (id or name, case-insensitive) shows one Notes field; without it every Notes field the record viewer shows the user is listed, empty ones included.
- Only the record's own Notes fields are listed. A Notes field that shows a linked record's notes isn't: open that record.
- `get-record` already gives each Notes field's count and newest 3 notes; this command gives them all.

**Output**

The record, then one block per Notes field, each note with its byline and its text indented:

```
Job 26-0073  (6adba16ca29a6293f946ca4f)

Job Notes (field 191656) — 2 notes
  Oct 26, 2026 at 9:47 AM — Pat Lee (you)  [6adf6815ca742e5ee6784809]
    Called the homeowner; install moved to Friday.
  Oct 25, 2026 at 4:10 PM — Ann Ray (edited)  [6adf5c11ca742e5ee67847f2]
    Measurements attached.
```

Lines to read:

- Field header: name, field id, note count; `(read-only for you)` marks a field the user can't add notes to (no Edit on the business object).
- Byline: when (the user's clock), the author, `(you)` on the user's own notes (only the author can edit or delete a note), `(edited)`, and the note id in brackets (`update-record-note` / `delete-record-note` take it).

`--quiet`: the note ids only.

**Common errors**

- `No field "X" on this business object. Notes fields: 191656 Job Notes, 191681 …` / `X is not a Notes field. Notes fields: …`: pick one of the Notes fields listed.
- `X shows a linked record's notes — add them on that record.`: list the linked record's notes instead.
- `<user> can't read the X notes of record … — the "X" (id) Notes field isn't visible to them in the record viewer, as in the web app.`: the user can't read that field in the web app either.

**Related:** `add-record-note`, `update-record-note`, `delete-record-note`, `get-record`, `list-record-messages`.

Related commands: [`list-record-files`](list-record-files.md), [`download-record-file`](download-record-file.md), [`list-record-messages`](list-record-messages.md), [`list-record-emails`](list-record-emails.md), [`list-record-tasks`](list-record-tasks.md), [`get-history-by-record`](../../../pathwd-history/references/commands/get-history-by-record.md), [`get-history-session`](../../../pathwd-history/references/commands/get-history-session.md), [`get-event-metadata`](../../../pathwd-history/references/commands/get-event-metadata.md).
