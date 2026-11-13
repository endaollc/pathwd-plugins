# list-feed

Runs with: path_my_work(command: "list-feed", args: "<flags>") (read-only).

```
Usage: list-feed [options]

List your dashboard Feed (newest first), like the web Feed page

Options:
  --unread          Only unread items
  --pinned          Only pinned items
  --bo <id|name>    Only items on this business object
  --record-id <id>  Only items on this record
  --title <text>    Title contains
  --content <text>  Content contains
  --page <n>        Page number (default: "1")
  --per-page <n>    Items per page (default: "25")
```

## Examples

- args: `--unread --per-page 20`

## Notes

Lists the signed-in user's dashboard Feed, newest first, like the web Feed page: task assignments, notification rules and actions, messages and system messages that reached them through the Feed. Popups are a separate list (`list-popups`); notification emails and texts aren't listed.

**Examples**

```
list-feed --unread --per-page 20
list-feed --record-id 6af3943cd0fe4220c7ad0f68
list-feed --bo Job --unread
list-feed --pinned
list-feed --title "Accounting Sync Failed" --page 2
```

- One page at a time: 25 by default; `--page 2`, `3`, … for more (there is no `--all`).
- The filters combine: `--unread`, `--pinned`, `--bo` (business object id or name), `--record-id` (24-character record id), `--title` / `--content` (contains, any case).
- Only the user's own Feed (`--as-user` reads another member's, for account Admins). Items on business objects the user can't view are left out.
- Unread counts without the items: `feed-unread-counts` (per business object; `feed-unread-counts --by record` per record).

**Output**

A table, newest first:

```
id     | at               | flags          | record                   | on                               | title                                           | content
-------|------------------|----------------|--------------------------|----------------------------------|-------------------------------------------------|--------------------
262615 | 2026-11-11 12:43 | unread         | 6af4b74be1333c2cca971692 | Receiving Truck Stop ZZY Scanne… | Task Scan Receiving Barcodes is assigned to you
262490 | 2026-11-10 16:05 | unread, pinned | 6ad13092e59d243cf0810058 | Job 26-1835                      | Deposit overdue                                 | Deposit for 26-1835 is 3 days late
262411 | 2026-11-10 09:12 |                | 6ad13092e59d243cf0810058 | Job 26-1835                      | New message from Ann Lee                        | Can we move the install to Friday?
[3 of 475 Feed items — --page 2 for more]
```

Columns to read:

- `id`: what `mark-notification-read` and `pin-notification` take.
- `at`: when it was sent (the user's clock). `flags`: `unread`, `pinned`.
- `record`: the record it is about (24 characters, for `get-record`, `list-feed --record-id`, `mark-all-notifications-read --record-id`); `on`: that record's business object and identifier.
- `title`, and `content` when it says more than the title (as text, cut at 80 characters).

`--quiet`: the ids only.

**Common errors**

- `Business object "X" not found — did you mean "…"?`: `--bo` takes the id or the singular or plural name.
- `[0 Feed items]`: nothing matches the filters (with `--record-id`, nothing about that record reached this user).

**Related:** `list-notifications`, `feed-records` (the records the Feed mentions), `feed-unread-counts`, `mark-notification-read`, `mark-all-notifications-read`, `pin-notification`, `list-popups`.

Related commands: [`list-user-tasks`](list-user-tasks.md), [`list-notifications`](list-notifications.md), [`feed-unread-counts`](feed-unread-counts.md), [`feed-records`](feed-records.md), [`list-popups`](list-popups.md).
