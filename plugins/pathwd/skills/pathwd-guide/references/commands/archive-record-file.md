# archive-record-file

Runs with: path_remove_from_record(command: "archive-record-file", args: "<flags>") (remove).

```
Usage: archive-record-file [options]

Archive a file on a record

Options:
  --record-id <id>  Record ID
  --file-id <id>    File ID
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --file-id 6a99b04605b6362500875af0`

## Notes

Archives a file on a record, as the file's Archive action on the Files tab does: the file stays on the record (`arch` is `yes` in `list-record-files`) but the web's Files list hides it until "show archived" is on. `unarchive-record-file` brings it back. To remove a file for good, `delete-record-file`.

**Examples**

```
archive-record-file --record-id 6ac54172dc615d8c41d303c3 --file-id 6ac541d90081b8686b3e0b2c
unarchive-record-file --record-id 6ac54172dc615d8c41d303c3 --file-id 6ac541d90081b8686b3e0b2c
```

- `--file-id` is the record file `id` from `list-record-files` (24 hex), not the stored file's UUID. `--record-id` is the record the file is on (for a linked record's file, the record id in its `on` column).
- Archiving keeps the file's types; `list-record-files` still lists it, with `arch` `yes`.
- The record's History shows it as File Archived.

**Output:** one line, `Archived file 6ac541d90081b8686b3e0b2c "shop-drawings.pdf" on record 6ac54172dc615d8c41d303c3` (`unarchive-record-file`: `Unarchived file …`). `--quiet`: the file id.

**Common errors**

- Not found (or a server error): the `--file-id` isn't a record file `id`; the stored file's UUID doesn't work here.
- `<user> can't archive a file on record … — they don't have View permission on <business object>, as in the web app.`

**Related:** `unarchive-record-file`, `delete-record-file`, `list-record-files`.

Related commands: [`delete-record-file`](delete-record-file.md), [`delete-record-note`](delete-record-note.md), [`delete-message`](delete-message.md), [`add-record-note`](add-record-note.md), [`upload-record-file`](upload-record-file.md), [`send-message`](send-message.md).
