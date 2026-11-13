# update-profile

Runs with: path_my_settings(command: "update-profile", args: "<flags>") (change).

```
Usage: update-profile [options]

Change your name or time zone, the way the profile settings modal saves them

Options:
  --first-name <name>  First name (also written to your Person record)
  --last-name <name>   Last name (also written to your Person record)
  --time-zone <zone>   account (the account's zone), local (this device's), or
                       an IANA zone such as America/Chicago
```

## Examples

- args: `--time-zone America/Chicago`

## Notes

Changes the signed-in user's own first name, last name or time zone, as the web app's profile settings save them. Email, password and phone aren't changed here (the notification phone: `update-notification-settings --phone`).

**Examples**

```
update-profile --time-zone America/Chicago
update-profile --time-zone account
update-profile --first-name Jane --last-name Smith
```

- `--first-name` / `--last-name` are also written to the user's Person record (its First Name / Last Name fields), as the web app does. Both names must end up non-blank.
- `--time-zone`: an IANA zone (`America/Chicago`, `America/New_York`, `Europe/London`), `account` (follow the account's zone; the default), or `local`. With `local` the web app follows each browser's zone, while commands read times in the zone of the machine that runs them; a named zone keeps the two in step.
- The time zone decides the clock commands read and show this user's dates on: task due dates (`create-task --due`), event times (`create-calendar-event --start`, `list-calendar-events`), date values typed in pathway screens. All-day events stay on the account's calendar.
- Another member's profile: `--as-user` (account Admins only).

**Output:** one line naming what changed, e.g. `Updated the profile of Jane Smith: first name "Jane", time zone America/Chicago`. `get-profile` shows the result:

```
user id: 8
name: Jane Smith
email: jane@example.com
phone: (512) 555-0142
time zone: America/Chicago
muted report emails: 3830
```

(`time zone: account (the account's zone)` or `local (this device's zone)` when no zone is named.)

**Common errors**

- `Nothing to change — give --first-name, --last-name or --time-zone`.
- `Unknown time zone "Central" — use account, local, or an IANA zone such as America/Chicago`: give the IANA name (`America/Chicago`, not "Central" or "Chicago").
- `First and last name are required`: a blank name was given (or the profile has no other name to keep).

**Related:** `get-profile`, `update-notification-settings`, `get-notification-settings`.

Related commands: [`mark-notification-read`](mark-notification-read.md), [`mark-all-notifications-read`](mark-all-notifications-read.md), [`pin-notification`](pin-notification.md), [`update-notification-settings`](update-notification-settings.md).
