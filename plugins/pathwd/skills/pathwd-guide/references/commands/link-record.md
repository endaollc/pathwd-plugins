# link-record

Runs with: path_create_record(command: "link-record", args: "<flags>") (add).

```
Usage: link-record [options]

Link two records via a relationship

Options:
  --record-id <id>          Source record ID
  --related-record-id <id>  Target record ID
  --relationship-id <id>    Relationship ID
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --related-record-id 6a95b8280ddb6fea5172f8b0 --relationship-id 33`

## Notes

Links one record to another on a relationship, as picking a record in a relationship card (or adding a row to a tab) does in the record viewer. It shows in both records' history.

**Examples**

```
link-record --record-id 6a9056455ff33756afba5084 --related-record-id 6a9056c05ff33756afba5138 --relationship-id 16480
link-record --record-id 6aa85464e0b8558669fe4182 --related-record-id 6a9056455ff33756afba5084 --relationship-id 16576
```

The first sets Job 26-0050's "Job Main Customer" (16480, Job → Customer); the second puts a Job Item on that Job ("JI Job", 16576, Job Item → Job).

- **The link is held by `--record-id`**: the record of the business object that owns the relationship (its `business_object_id` in `list-relationships`). `--related-record-id` is the record on the relationship's `to_business_object_id` side.
- **Adding a row to a tab is linked from the row's side.** "Add this Job Item to the Job" is the Job Item's relationship (`--record-id` = the Job Item). The Job's "Job Items" tab is the inverse view of that same relationship id; the Job holds nothing to link.
- A record holds **one** record per relationship. Linking where a link already exists **replaces** it (the old record loses the link in its history, and its totals update); many records can link to the same target.
- `--relationship-id` takes only an id: `list-relationships --bo-name "Job Item"` (the relationships that business object holds), or the `relationship_id` in `list-related-records --record-id <id>`. Relationships by name aren't accepted here.
- Totals and counts on the linked record (aggregates) and relationship fields recalculate after the link, sometimes a moment later.

**Output**

```
Linked record 6a9056455ff33756afba5084 (Job 26-0050) to record 6a9056c05ff33756afba5138 (Customer Smith Plumbing) on Job Main Customer (16480)
```

- One line: the record holding the link, the record linked (with its name when the saved record carries it) and the relationship. `get-record --record-id <id>` shows the link under `Related:`.

**Common errors**

- An error ending `The relationship 16576 does not belong to the record business object`: the relationship belongs to the other record's business object. Swap `--record-id` and `--related-record-id`.
- An error ending `Relationship record with id … not found`: `--related-record-id` isn't a record in this account.
- `<user> can't link record … on relationship 16480 — they don't have Edit permission on Jobs (business object 5050), as in the web app.`
- `<user> can't link record … on relationship 16480 — the "Job Main Customer" relationship (16480) isn't visible to them in the record viewer, as in the web app.`
- A record number ("Job 26-0050") in an id flag fails with `ERROR [404]: Not Found`; find the id with `search`.

**Related:** `unlink-record`, `list-related-records` (check the link), `list-relationships`, `create-record` (create first, then link). A pathway that creates a linked record (a Job Item from its Job) usually links it too.

Related commands: [`create-record`](create-record.md), [`archive-record`](archive-record.md), [`unlink-record`](unlink-record.md).
