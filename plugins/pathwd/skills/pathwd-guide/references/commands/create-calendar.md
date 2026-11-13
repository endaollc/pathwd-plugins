# create-calendar

Runs with: path_manage_calendars(command: "create-calendar", args: "<flags>") (change).

```
Usage: create-calendar [options]

Create a calendar (Admins and Calendar Admins, as the web's Add Calendar form).
You become its owner; everyone can view it until you change --all-users
(update-calendar) or share it (share-calendar)

Options:
  --name <name>  Calendar name (unique in the account)
  --color <hex>  Color, a hex like #1A5E20 (default #1A5E20, the web's preset)
```

## Notes

Creates a calendar, as the web's Manage Calendars → Add Calendar form does. Admins and Calendar Admins only. The creator becomes its owner, and every member can view it until that is changed.

**Examples**

```
create-calendar --name "Install Crew 2"
create-calendar --name "Install Crew 2" --color "#1565C0"
```

- `--name` must be unique in the account; `--color` is a hex such as `#1565C0` (default `#1A5E20`, the web's preset).
- A new calendar is a standard PATH calendar; merging it with Google / Office 365 is done in the web app's calendar settings.
- After creating: `update-calendar <calendar> --all-users edit` (or `none`) sets what every member gets; `share-calendar` gives users and roles view or edit, or hands ownership to someone else.
- A calendar group (several calendars shown together) is `create-calendar-group`.

**Output:** `Created calendar 2551 "Install Crew 2" (#1565C0)`; `--quiet` prints the id.

**Common errors**

- `<user> can't create calendar "Install Crew 2" — only Admins and Calendar Admins create calendars and calendar groups, as in the web app.`
- `--color must be a hex color like #1A5E20 (got "blue")`.
- `The calendar with name Install Crew 2 already exists for account`: pick another name.
- `--name can't be blank`.

**Related:** `share-calendar`, `update-calendar`, `get-calendar`, `list-calendars`, `delete-calendar`, `create-calendar-group`.

Related commands: [`update-calendar`](update-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`update-calendar-event`](update-calendar-event.md), [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`clear-appointment`](clear-appointment.md).
