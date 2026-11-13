# update-notification-settings

Runs with: path_my_settings(command: "update-notification-settings", args: "<flags>") (change).

```
Usage: update-notification-settings [options]

Turn your notification channels on or off, or change your notification phone
number

Options:
  --set <key.medium=on|off>  e.g. task.email=off or notification:42.text=on
                             (repeatable; keys from get-notification-settings
                             [--resolved])
  --phone <number>           The phone number text notifications go to ("" to
                             remove)
  --data <json>              Raw request body: { settings: [...], phone_number?
                             } (as the web sends it)
```

## Notes

Turns the signed-in user's own notification channels on or off, or changes the phone number text notifications go to: the notification part of the web app's profile settings. It changes only that user's settings, never the account's notification rules.

**Examples**

```
update-notification-settings --set task_assignee.email=on
update-notification-settings --set notification:1857.email=on --set notification:1857.feed=off
update-notification-settings --set action:6370.pop_up=on
update-notification-settings --phone "(512) 555-0142" --set task_assignee.text=on
```

- A `--set` key is `<entity_type>[:<entity_id>].<channel>=on|off`, written as `get-notification-settings` shows the row key. Channels: `feed`, `email`, `text`, `mobile`, `pop_up`. `--set` repeats; several channels of one row are saved together.
- Account-wide rows have no id: `task_assignee` (tasks assigned to the user), `messages`, `system_messages`. Per-notification rows carry one: `notification:<id>` (a business object notification rule), `action:<id>` (a notification action), `inbound_email:<business object id>`.
- `get-notification-settings --resolved` lists every notification that can reach the user, with each channel `on`/`off` when it is the user's choice, `req` (required, always on) or `-` (not allowed) when the account decides. Only `on`/`off` channels can be changed.

  ```
  key                | name          | bo            | feed | email | text | mobile | popup
  -------------------|---------------|---------------|------|-------|------|--------|------
  action:10160       | ST2-F08       | Schedule Test | req  | -     | -    | -      | -
  inbound_email:5050 | Inbound Email | Job           | on   | off   | off  | off    | off
  ```

  Notifications whose rule is switched off in the Designer send nothing and are left out; `get-notification-settings --resolved --include-disabled` lists them too, flagged `rule-off`. `get-notification-settings` alone lists the account-wide rows (`task_assignee`, `messages`, …) and the user's saved rows, with `*` on a channel the account locks (`off*`).
- `--phone` sets the number texts go to; `--phone ""` removes it.
- Another member's settings: `--as-user` (account Admins only).

**Output:** one line naming what changed, e.g. `Updated the notification settings of Ann Lee: notification:1857.email on, notification:1857.feed off, phone (512) 555-0142`.

**Common errors**

- `Can't read --set "…" — use <entity_type>[:<entity_id>].<feed|email|text|mobile|pop_up>=on|off…`: e.g. `sms` instead of `text`, `popup` instead of `pop_up`, or a missing `=on`.
- `No notification setting "notification:42" — see get-notification-settings [--resolved]`: no such row reaches this user.
- `email on notification:3147 is Not Allowed by your account's settings and can't be turned on` (or `is Required … can't be turned off`): the notification's own setup decides that channel.
- `feed on messages is locked by your account's settings`: an account-wide channel the account locks.
- `Nothing to change — give --set, --phone or --data`.

**Related:** `get-notification-settings`, `get-profile`, `update-profile`, `list-feed`, `list-popups`.

Related commands: [`mark-notification-read`](mark-notification-read.md), [`mark-all-notifications-read`](mark-all-notifications-read.md), [`pin-notification`](pin-notification.md), [`update-profile`](update-profile.md).
