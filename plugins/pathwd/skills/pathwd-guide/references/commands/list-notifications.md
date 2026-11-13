# list-notifications

Runs with: path_my_work(command: "list-notifications", args: "<flags>") (read-only).

```
Usage: list-notifications [options]

List notifications

Options:
  --page <n>      Page number (default: "1")
  --per-page <n>  Items per page (default: "15")
  --unread        Only show unread notifications
```

## Examples

- args: `--unread`

## Notes

Lists the signed-in user's Feed notifications, newest first. It reads the same list as `list-feed`, with fewer filters and 15 a page; `list-feed` is usually the better choice. Popups are a separate list (`list-popups`).

**Examples**

```
list-notifications
list-notifications --unread --per-page 50
list-notifications --page 2 --fields id,at,title
```

- One page at a time: 15 by default, `--page 2`, `3`, … for more (the footer says how many there are: `[15 of 456 notifications — --page 2 for more]`).
- `--unread`: only items not yet read in the Feed.
- Only the user's own notifications, never another member's (`--as-user` reads theirs, for account Admins). Notifications on business objects the user can't view are left out.

**Output**

A table, newest first:

```
id     | at               | flags          | record                   | on                               | title                                           | content
-------|------------------|----------------|--------------------------|----------------------------------|-------------------------------------------------|--------------------
262615 | 2026-11-11 12:43 | unread         | 6af4b74be1333c2cca971692 | Receiving Truck Stop ZZY Scanne… | Task Scan Receiving Barcodes is assigned to you
262490 | 2026-11-10 16:05 | unread, pinned | 6ad13092e59d243cf0810058 | Job 26-1835                      | Deposit overdue                                 | Deposit for 26-1835 is 3 days late
262411 | 2026-11-10 09:12 |                | 6ad13092e59d243cf0810058 | Job 26-1835                      | New message from Ann Lee                        | Can we move the install to Friday?
[3 of 475 notifications — --page 2 for more]
```

Columns to read:

- `id`: what `mark-notification-read` and `pin-notification` take.
- `at`: when it was sent (the user's clock). `flags`: `unread`, `pinned`.
- `record`: the record it is about (24 characters, for `get-record`, `list-feed --record-id`, `mark-all-notifications-read --record-id`); `on`: that record's business object and identifier.
- `title`, and `content` when it says more than the title (as text, cut at 80 characters).

`--quiet`: the ids only.

**Common errors:** none particular to this command; an empty list on `--unread` means everything is read.

**Related:** `list-feed` (filters by record, business object, pinned, text), `feed-unread-counts`, `mark-notification-read`, `mark-all-notifications-read`, `list-popups`.

Related commands: [`list-user-tasks`](list-user-tasks.md), [`list-feed`](list-feed.md), [`feed-unread-counts`](feed-unread-counts.md), [`feed-records`](feed-records.md), [`list-popups`](list-popups.md).
