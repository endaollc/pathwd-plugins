# update-calendar

Runs with: path_manage_calendars(command: "update-calendar", args: "<flags>") (change).

```
Usage: update-calendar [options] <calendar>

Rename or recolor a calendar, or set what All Users get (view, edit or none) —
the calendar's owner or an Admin / Calendar Admin

Arguments:
  calendar             Calendar id or name

Options:
  --name <name>        New name
  --color <hex>        New color, a hex like #1A5E20
  --all-users <level>  What every member gets without another grant: view (the
                       default for a new calendar), edit, or none (only people
                       and roles it is shared with)
```

Related commands: [`create-calendar`](create-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`update-calendar-event`](update-calendar-event.md), [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`clear-appointment`](clear-appointment.md).
