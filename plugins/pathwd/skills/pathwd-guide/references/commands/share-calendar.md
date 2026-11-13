# share-calendar

Runs with: path_manage_calendars(command: "share-calendar", args: "<flags>") (change).

```
Usage: share-calendar [options] <calendar>

Share a calendar with users and/or roles (view or edit its events), make one
user its owner, or remove their access — the calendar's owner or an Admin /
Calendar Admin, as the web's Permissions tab

Arguments:
  calendar          Calendar id or name

Options:
  --users <list>    Comma-separated users: ids, emails or full names
  --roles <list>    Comma-separated roles: ids or names ("All Users" sets what
                    everyone gets)
  --access <level>  view, edit (implies view), owner (one user; the previous
                    owner keeps view/edit), or remove (drop their row)
```

## Examples

- args: `Installs --roles Sales --access view`

## Notes

Changes who may view or edit a calendar's events, as the calendar's Permissions tab does: shares it with users and roles, sets what "All Users" get, hands ownership to one user, or removes someone's access. The calendar's owner, Admins and Calendar Admins may do this; an Edit grant isn't enough.

**Examples**

```
share-calendar "Outside Sales" --roles "Sales Manager" --access edit
share-calendar 1626 --users "jane@example.com,Bob Smith" --access view
share-calendar "Outside Sales" --users "Bob Smith" --access remove
share-calendar "Outside Sales" --roles "All Users" --access remove
share-calendar "Install Crew 2" --users jane@example.com --access owner
```

- The calendar comes first, by id or name. `--users` takes ids, emails or full names; `--roles` ids or names (`list-roles`); both are comma-separated and can be given together.
- `--access`: `view`; `edit` (includes view); `remove` (drops their row, so they fall back to what "All Users" get); `owner` (exactly one user, no roles: they become the owner and the previous owner keeps view/edit).
- The "All Users" role sets what every member gets without another grant: `--roles "All Users" --access edit`, `view`, or `remove` (no access except the users and roles listed). `update-calendar <calendar> --all-users view|edit|none` does the same.
- Calendar access is separate from record access: sharing a calendar lets people see and change its events, including record appointments shown there, but changing an appointment also needs Edit on the record's business object.
- Calendar groups are shared with `share-calendar-group`; a group's sharing never changes access to the calendars inside it.

**Output:** one line: `Shared calendar 1626 "Outside Sales" with Sales Manager (role): edit`, `Removed Bob Smith from calendar 1626 "Outside Sales"`, or `Jane Smith is now the owner of calendar …`. `get-calendar <id>` shows everyone it is shared with.

**Common errors**

- `<user> can't change who calendar "Outside Sales" is shared with — only the calendar's owner or an Admin / Calendar Admin can manage calendar "Outside Sales", as in the web app.`
- `No user "X" in this account (give their name as list-users shows it, their email or their id)`; `"X" is the name of 2 users in this account; give that user's email address instead.`; `<name> is deactivated`; `No role "X" in this account (see list-roles)`.
- `--access must be view, edit, owner or remove`; `Give --users and/or --roles to share with`.
- `--access owner takes exactly one user (--users) and no roles…`; `Owner is user-only: give one user with --users`.
- `Unknown calendar "X" (or one this user can't see). Calendars: …`.

**Related:** `get-calendar` (current sharing), `update-calendar`, `create-calendar`, `list-calendars`, `share-calendar-group`, `list-users`, `list-roles`.

Related commands: [`create-calendar`](create-calendar.md), [`update-calendar`](update-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`update-calendar-event`](update-calendar-event.md), [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`clear-appointment`](clear-appointment.md).
