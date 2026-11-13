# mark-all-notifications-read

Runs with: path_my_settings(command: "mark-all-notifications-read", args: "<flags>") (change).

```
Usage: mark-all-notifications-read [options]

Mark every unread Feed item read (the Feed page "Mark all as read"), or one
record's with --record-id

Options:
  --record-id <id>  Only this record's items
  --mediums <list>  Comma-separated: feed, popup, email, sms, mobile (default
                    feed) (default: "feed")
```

Related commands: [`mark-notification-read`](mark-notification-read.md), [`pin-notification`](pin-notification.md), [`update-notification-settings`](update-notification-settings.md), [`update-profile`](update-profile.md).
