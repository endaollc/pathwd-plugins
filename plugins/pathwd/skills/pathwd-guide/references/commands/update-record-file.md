# update-record-file

Runs with: path_update_record(command: "update-record-file", args: "<flags>") (change).

```
Usage: update-record-file [options]

Update a file on a record

Options:
  --record-id <id>  Record ID
  --file-id <id>    File ID
  --data <json>     JSON body with fields to update
```

## Notes

Changes a record file's file types and comment, as the file's Edit form on the Files tab does. It can't rename a file or replace its content (upload a new file for that).

**Examples**

```
update-record-file --record-id 6ac54172dc615d8c41d303c3 --file-id 6ac542b6dc615d8c41d30e85 --data '{"file_type_ids":[15882],"comment":"Signed by the homeowner on 2026-11-10"}'
update-record-file --record-id 6ac54172dc615d8c41d303c3 --file-id 6ac541e60081b8686b3e0b41 --data '{"file_type_ids":[15884,15883]}'
```

- `--file-id` is the record file `id` from `list-record-files` (24 hex), not the stored file's UUID; `--record-id` is the record the file is on (for a linked record's file, the record id in its `on` column, not the record you listed).
- `--data` holds `file_type_ids` (required, an array of type ids) and `comment`. The types given replace the file's types (`[]` removes them all). They are types of the file's own business object: `list-file-types --bo-id <its business object id> --include-hidden` gives the ids and names; `list-record-files` shows the file's current types by name (`type`), to keep them add their ids to the list.
- `comment` replaces the file's comment, and leaving it out clears it: send the current comment again to keep it.
- Giving a file an exclusive type takes that type off the record's other files.

**Output:** one line, `Updated file 6ac542b6dc615d8c41d30e85 "Contract.pdf" on record 6ac54172dc615d8c41d303c3: file types, comment` (the keys sent). `--quiet`: the file id.

**Common errors**

- A validation error naming `file_type_ids`: the key is missing or isn't an array of numbers.
- Not found (or a server error): the `--file-id` isn't a record file `id`; the stored file's UUID doesn't work here.
- `<user> can't update a file on record … — they don't have View permission on <business object>, as in the web app.`

**Related:** `list-record-files`, `list-file-types`, `upload-record-file`, `archive-record-file`.

Related commands: [`update-record`](update-record.md), [`unarchive-record`](unarchive-record.md), [`unarchive-record-file`](unarchive-record-file.md), [`set-appointment`](set-appointment.md), [`update-record-note`](update-record-note.md), [`update-message`](update-message.md).
