# delete-record-file

Runs with: path_remove_from_record(command: "delete-record-file", args: "<flags>") (remove).

```
Usage: delete-record-file [options]

Delete a file from a record. Asks for confirmation (--confirm <code>)

Options:
  --record-id <id>  Record ID
  --file-id <id>    File ID
```

## Notes

Deletes a file from a record for good, as the file's Delete on the Files tab does. It can't be undone; `archive-record-file` is the reversible way to put a file away. Only Admins and users with the Delete Files permission can delete files.

**Examples**

```
delete-record-file --record-id 6ac54172dc615d8c41d303c3 --file-id 6ac542980081b8686b3e0da9
delete-record-file --record-id 6ac54172dc615d8c41d303c3 --file-id 6ac542980081b8686b3e0da9 --confirm DEL-3F9A1C07
```

- `--file-id` is the record file `id` from `list-record-files` (24 hex), not the stored file's UUID. `--record-id` is the record the file is on (for a linked record's file, the record id in its `on` column).
- **Two calls.** The first deletes nothing: it exits with code 3 and says what it would delete, with a confirmation code:

  ```
  NOT DELETED: this needs confirmation.
  It would permanently delete file 6ac542980081b8686b3e0da9 "historic_casement_shop_drawings.pdf" on Quote Full Quote: 26-0095-01 (6ac54172dc615d8c41d303c3) in account 1668.
  ```

  Show that to the user; once they say yes, run the same command again with `--confirm <code>`. The code works only for this exact command and arguments, and expires within 20 minutes; a changed command needs a new code.

**Output:** one line, `Deleted file 6ac542980081b8686b3e0da9 from record 6ac54172dc615d8c41d303c3`. `--quiet`: the file id. The record's History shows the deletion.

**Common errors**

- `<user> can't delete file … — none of their roles has the Delete Files permission, as in the web app.`: archive it instead, or ask an Admin.
- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: the command changed or the code is old; run it without `--confirm` for a new code.
- Not found (or a server error): the `--file-id` isn't a record file `id`.

**Related:** `archive-record-file`, `list-record-files`.

Related commands: [`archive-record-file`](archive-record-file.md), [`delete-record-note`](delete-record-note.md), [`delete-message`](delete-message.md), [`add-record-note`](add-record-note.md), [`upload-record-file`](upload-record-file.md), [`send-message`](send-message.md).
