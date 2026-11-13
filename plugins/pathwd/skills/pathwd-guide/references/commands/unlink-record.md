# unlink-record

Runs with: path_archive_record(command: "unlink-record", args: "<flags>") (remove).

```
Usage: unlink-record [options]

Unlink a record from its target on a given relationship

Options:
  --record-id <id>        Source record ID
  --relationship-id <id>  Relationship ID to clear
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --relationship-id 33`

## Notes

Removes a record's link on one relationship, as the remove control on a relationship card does in the record viewer. Neither record is archived or deleted; only the link goes, and both records' history shows it.

**Examples**

```
unlink-record --record-id 6a9056455ff33756afba5084 --relationship-id 16476
unlink-record --record-id 6aa85464e0b8558669fe4182 --relationship-id 16576
```

The first clears Job 26-0050's "Job Main Contact" (16476); the second takes a Job Item off its Job ("JI Job", 16576).

- **Run it on the record that holds the link**: the record of the business object that owns the relationship. There is no `--related-record-id`: a record holds one link per relationship, so the relationship alone says which link to clear.
- **To take a row off a tab, unlink the row.** A Job's "Job Items" tab lists Job Items that link to the Job, so the Job Item is `--record-id`. Find the rows and their ids with `list-related-records --record-id <job id> --relationship-name "Job Items"`.
- `--relationship-id` takes only an id: `list-relationships --bo-name <business object>` or `list-related-records --record-id <id>` (a `direct` row there is a link this record holds).
- Unlinking a relationship with nothing linked isn't refused (the record still gets an "unlinked" history entry), so check with `list-related-records` first.
- Totals and counts on the formerly linked record recalculate; relationship fields drawn through the link go empty. Milestone criteria and pathways that depend on the link can change.
- Put the link back with `link-record`.

**Output**

```
Unlinked record 6a9056455ff33756afba5084 (Job 26-0050) from record 6ace8be8b2afe301a7d3db8e on Job Main Contact (16476)
```

- One line: the record, the record it was linked to (when it was read first) and the relationship cleared.

On the PATH WD Remote MCP, unlinking counts as a removal: a credential with deletes off answers `This agent credential may not run "unlink-record" (deletes are off).`

**Common errors**

- An error ending `The relationship 16576 does not belong to the record business object`: `--record-id` is on the other side; unlink from the record that holds the link.
- `<user> can't unlink record … on relationship 16476 — they don't have Edit permission on Jobs (business object 5050), as in the web app.`
- `<user> can't unlink record … on relationship 16476 — the "Job Main Contact" relationship (16476) isn't visible to them in the record viewer, as in the web app.`

**Related:** `link-record`, `list-related-records`, `archive-record` (takes a record out of lists and totals without touching its links).

Related commands: [`archive-record`](archive-record.md), [`create-record`](create-record.md), [`link-record`](link-record.md).
