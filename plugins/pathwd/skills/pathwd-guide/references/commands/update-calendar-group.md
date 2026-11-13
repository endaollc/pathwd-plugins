# update-calendar-group

Runs with: path_manage_calendars(command: "update-calendar-group", args: "<flags>") (change).

```
Usage: update-calendar-group [options] <group>

Rename a calendar group or change its calendars (Edit on the group), or set
what All Users get (the group's owner or an Admin / Calendar Admin)

Arguments:
  group                      Calendar group id or name

Options:
  --name <name>              New name
  --calendars <list>         Replace its calendars (comma-separated ids or
                             names)
  --add-calendars <list>     Add calendars
  --remove-calendars <list>  Remove calendars
  --all-users <level>        What every member gets on the group without
                             another grant: view, edit or none
```

Related commands: [`create-calendar`](create-calendar.md), [`update-calendar`](update-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`update-calendar-event`](update-calendar-event.md), [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`clear-appointment`](clear-appointment.md).
