# list-calendars

Runs with: path_calendars(command: "list-calendars", args: "<flags>") (read-only).

```
Usage: list-calendars [options]

List calendars: id, name, synced (google / outlook when merged with one),
owner. get-calendar <id> shows who it is shared with

Options:
```

## Notes

Lists the calendars the signed-in user can see: the calendar list of the web app's Calendar page. Calendar groups are listed separately (`list-calendar-groups`).

**Examples**

```
list-calendars
list-calendars --fields id,name
```

- Every visible calendar at once (no paging). A user sees a calendar when it is shared with them or their role, or when its "All Users" level is view or edit (a new calendar's default); Admins and Calendar Admins see every calendar.
- Commands that take a calendar (`create-calendar-event --calendar`, `set-appointment --calendar`, `share-calendar`, `delete-calendar`) accept its id or its name.

**Output**

```
id   | name             | synced  | owner
-----|------------------|---------|-------------
1625 | Demo             | google  | MM Admin
1626 | Outside Sales    |         | Todd Demo
1623 | Delivery Truck 1 | outlook | Endao System
[13 calendars]
```

- Columns to read: `id`, `name`, `synced` (`google` / `outlook` for a calendar created as a merged one; blank for a PATH calendar — a PATH calendar can also be merged later, and its synced events show `from: outlook` in `list-calendar-events`), `owner` (who manages it, with Admins and Calendar Admins).
- Who else it is shared with, and what "All Users" get: `get-calendar <id|name>`.

**Common errors:** none particular to this command; a calendar missing from the list is one the user can't view.

**Related:** `get-calendar`, `list-calendar-events --calendar-ids`, `list-calendar-groups`, `create-calendar`, `share-calendar`, `list-appointment-fields`.

Related commands: [`get-calendar`](get-calendar.md), [`list-calendar-groups`](list-calendar-groups.md), [`get-calendar-group`](get-calendar-group.md), [`list-calendar-events`](list-calendar-events.md), [`get-calendar-event`](get-calendar-event.md).
