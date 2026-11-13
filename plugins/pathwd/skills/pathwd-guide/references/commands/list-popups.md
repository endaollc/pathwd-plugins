# list-popups

Runs with: path_my_work(command: "list-popups", args: "<flags>") (read-only).

```
Usage: list-popups [options]

List your popup notifications (the in-app popups)

Options:
  --unread        Only unread popups
  --page <n>      Page number (default: "1")
  --per-page <n>  Items per page (default: "25")
```

## Notes

Lists the signed-in user's unread popup notifications: the in-app popups a notification rule or action sends when its Pop Up channel is on. Feed items are a separate list (`list-feed`).

**Examples**

```
list-popups
list-popups --per-page 50 --page 2
```

- Only unread popups are listed, with or without `--unread`; a popup leaves the list once it is marked read (`mark-notification-read <id> --popup`). Read popups can't be listed.
- One page at a time: 25 by default; `--page 2`, `3`, … for more.
- Only the user's own popups (`--as-user` reads another member's, for account Admins).
- Read state is kept per channel: marking a popup read doesn't mark the Feed copy of the same notification, and the reverse.

**Output**

The same table as `list-feed`: `id` (for `mark-notification-read <id> --popup`), `at`, `flags`, `record`, `on`, `title`, `content` (when it says more than the title), with a `[N of TOTAL popups — --page 2 for more]` footer when there are more. `[0 popups]` means no unread popups. `--quiet`: the ids only.

**Common errors:** none particular to this command.

**Related:** `mark-notification-read --popup`, `list-feed`, `get-notification-settings --resolved` (which notifications may pop up), `update-notification-settings`.

Related commands: [`list-user-tasks`](list-user-tasks.md), [`list-notifications`](list-notifications.md), [`list-feed`](list-feed.md), [`feed-unread-counts`](feed-unread-counts.md), [`feed-records`](feed-records.md).
