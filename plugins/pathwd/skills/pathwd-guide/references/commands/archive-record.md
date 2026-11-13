# archive-record

Runs with: path_archive_record(command: "archive-record", args: "<flags>") (remove).

```
Usage: archive-record [options]

Archive a record (or several with --record-ids) via the Archive Record step

Options:
  --record-id <id>    Record ID
  --record-ids <ids>  Comma-separated record IDs
  --bo-id <id>        Business object ID (skips the lookup; only with a single
                      --record-id)
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084`

## Notes

Archives records, as the record viewer's Archive button (and a pathway's Archive Record step) does. An archived record is kept and can be brought back with `unarchive-record`; it isn't deleted. These commands have no way to delete a record.

**Examples**

```
archive-record --record-id 6aa85470e0b8558669fe420c
archive-record --record-ids 6aa85470e0b8558669fe420c,6aa8559df6a1d266e4a0f852,6aa856c6f6a1d266e4a10ae5
```

- `--record-ids` takes a comma-separated list (no spaces needed); `--record-id` and `--record-ids` can be combined. The records are archived one after another, and one failure doesn't stop the rest.
- Each record's business object is read from the record, so records of different business objects can be mixed. `--bo-id` is accepted with a single `--record-id` but changes nothing, since the record is read anyway.
- What archiving does: the record drops out of search, folders and reports (unless one is set to include archived records), stops counting in totals and counts on the records it links to, and its appointments are archived with it. Its links, field values, files and history stay, and `get-record` still opens it (with `archived`).
- The command doesn't ask for confirmation. When the user said "delete", confirm that archiving is what they want and which records, and list them (`find-records`, `list-related-records`) before archiving several.

**Output**

```
Archived record 6aa85470e0b8558669fe420c (Job Item 100-3)
Already archived: record 6aa8559df6a1d266e4a0f852 (Job Item 100-4)
Not archived: record 6aa856c6f6a1d266e4a10ae5 — <user> can't archive record … — they don't have Archive permission on Job Items (business object 5063), as in the web app.
[1 archived, 1 already archived, 1 failed]
```

- One line per record (`Archived`, `Already archived` — left as it was — or `Not archived` with the reason), and a count line when there are several.
- When any record failed, the command exits with an error status after printing every result; the others are still archived.

On the PATH WD Remote MCP, archiving counts as a removal: a credential with deletes off answers `This agent credential may not run "archive-record" (deletes are off).`

**Common errors**

- `Pass --record-id <id> or --record-ids <id,id,...>.`
- `--bo-id only applies to a single --record-id.`
- `Not archived: record … — <user> can't archive record … — they don't have Archive permission on Job Items (business object 5063), as in the web app.` (Archive on the business object, or the Delete Records role permission, allows it.)
- `Not archived: record … — … Not Found`: not a record id in this account (a record number such as "Job 26-0050" isn't an id; find it with `search`).

**Related:** `unarchive-record`, `search --include-archived` (find archived records), `get-record` (`[archived]` in the header; `get-record --raw` shows when), `archive-record-file` (a file, not a record).

Related commands: [`unlink-record`](unlink-record.md), [`create-record`](create-record.md), [`link-record`](link-record.md).
