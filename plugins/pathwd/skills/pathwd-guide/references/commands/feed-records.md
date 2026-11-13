# feed-records

Runs with: path_my_work(command: "feed-records", args: "<flags>") (read-only).

```
Usage: feed-records [options]

The records your Feed and popups mention (the Feed page record list)

Options:
  --bo <id|name>  Only records of this business object
```

## Notes

Lists the records the signed-in user's Feed and popups mention: the record list beside the web Feed page. Each record appears once, however many items are about it.

**Examples**

```
feed-records
feed-records --bo 5050
feed-records --bo Jobs
```

- `--bo` takes the business object's id or its singular or plural name, as `list-feed --bo` does.
- Every record comes back at once (no paging); records on business objects the user can't view are left out.
- The items about one record: `list-feed --record-id <record id>`; unread counts per record: `feed-unread-counts --by record`.

**Output**

A table:

```
record                   | bo          | name
-------------------------|-------------|-----------------------------------------
6a905877182de742a298fda9 | Quote       | Full Quote: 26-0050-01
6a90b3f7182de742a2998563 | Job         | 26-0053: SD PostWipe Retail 3
6a95b56babc978b6acadc059 | Appointment | Pierce Residence: Robby Pierce: Delivery
[3 records]
```

Columns to read: `record` (24 characters, for `get-record` and `list-feed --record-id`), `bo` (its business object), `name` (the record's identifier as the web app shows it). `--quiet`: the record ids only.

**Common errors**

- `Business object "X" not found — did you mean "…"?`: no business object of that name the user can view; `list-business-objects` lists the names.

**Related:** `list-feed --record-id`, `feed-unread-counts`, `mark-all-notifications-read --record-id`, `get-record`.

Related commands: [`list-user-tasks`](list-user-tasks.md), [`list-notifications`](list-notifications.md), [`list-feed`](list-feed.md), [`feed-unread-counts`](feed-unread-counts.md), [`list-popups`](list-popups.md).
