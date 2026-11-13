# share-calendar-group

Runs with: path_manage_calendars(command: "share-calendar-group", args: "<flags>") (change).

```
Usage: share-calendar-group [options] <group>

Share a calendar group with users and/or roles (view, or edit its name and
calendars), make one user its owner, or remove their access — the group's owner
or an Admin / Calendar Admin. Doesn't change access to the calendars inside it

Arguments:
  group             Calendar group id or name

Options:
  --users <list>    Comma-separated users: ids, emails or full names
  --roles <list>    Comma-separated roles: ids or names ("All Users" sets what
                    everyone gets)
  --access <level>  view, edit (implies view), owner (one user; the previous
                    owner keeps view/edit), or remove
```

Related commands: [`create-calendar`](create-calendar.md), [`update-calendar`](update-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`update-calendar-event`](update-calendar-event.md), [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`clear-appointment`](clear-appointment.md).
