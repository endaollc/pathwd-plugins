# unarchive-record

Runs with: path_update_record(command: "unarchive-record", args: "<flags>") (change).

```
Usage: unarchive-record [options]

Unarchive a record

Options:
  --record-id <id>  Record ID
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084`

## Notes

Brings an archived record back, as Unarchive in the record viewer does: it shows in search, folders and reports again and counts again in the totals of the records it links to. One record per run.

**Examples**

```
search --query "26-0052" --bo-name Job --include-archived
find-records --bo-name "Job Item" --logic '[Line #] = "100-3"' --include-archived
unarchive-record --record-id 6a9072ad182de742a29942f1
```

- Archived records are left out of `search` and `find-records` unless `search --include-archived` / `find-records --include-archived` asks for them (`search` covers only business objects shown in the menu, `find-records` any one). `get-record` opens an archived record by id (`[archived]` in its header).
- Its appointments come back with it, and totals on linked records recalculate. Links were kept while it was archived, so nothing needs relinking.
- There is no `--record-ids`: unarchive several records one run each.

**Output**

```
Unarchived record 6a9072ad182de742a29942f1 (Job 26-0052)
```

**Common errors**

- `<user> can't unarchive record … — they don't have Archive permission on Jobs (business object 5050), as in the web app.` The same permission as archiving: Archive on the business object, or the Delete Records role permission.
- `ERROR [404]: Not Found`: not a record id in this account (a record number such as "Job 26-0050" isn't an id).

**Related:** `archive-record`, `search --include-archived`, `find-records --include-archived`, `get-record`, `unarchive-record-file` (a file, not a record).

Related commands: [`update-record`](update-record.md), [`unarchive-record-file`](unarchive-record-file.md), [`set-appointment`](set-appointment.md), [`update-record-note`](update-record-note.md), [`update-record-file`](update-record-file.md), [`update-message`](update-message.md).
