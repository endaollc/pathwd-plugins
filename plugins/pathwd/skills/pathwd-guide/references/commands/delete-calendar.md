# delete-calendar

Runs with: path_delete_calendar_items(command: "delete-calendar", args: "<flags>") (remove).

```
Usage: delete-calendar [options] <calendar>

Delete a calendar and its events (record appointments are cleared), or move the
events to another calendar first — the calendar's owner or an Admin / Calendar
Admin. Asks for confirmation (--confirm <code>)

Arguments:
  calendar                     Calendar id or name

Options:
  --move-events-to <calendar>  Keep the events: move every one to this calendar
                               (one you can edit); record appointments follow
  --external-events <choice>   A calendar merged with Google / Office 365:
                               delete the events PATH created there too, or
                               leave them (default leave)
```

## Notes

Deletes a calendar permanently, as Manage Calendars → Delete does: its events are deleted with it (record appointments on them are cleared), or first moved to another calendar. The calendar's owner, Admins and Calendar Admins may do this. Deleting can't be undone; the user confirms first.

**Examples**

```
delete-calendar "Install Crew 2"
delete-calendar "Install Crew 2" --move-events-to "Outside Sales"
delete-calendar 2085 --external-events delete
delete-calendar "Install Crew 2" --move-events-to "Outside Sales" --confirm DEL-7B04D2E9
```

- Two calls. The first deletes nothing: it prints what would happen (the calendar, how many events are deleted or moved, and for a merged calendar what happens on Google / Office 365) and a code. After the user explicitly says yes, the same command, with the same flags, and `--confirm <code>` deletes. The code works only for that exact command and expires within 20 minutes.
- `--move-events-to` (id or name) keeps the events: every one moves to that calendar, and record appointments follow. The user needs Edit on that calendar.
- Without it, every event is deleted and each record appointment on them becomes Not Set.
- `--external-events` matters only for a calendar merged with Google / Office 365: it is disconnected; `delete` also deletes the events PATH created there, `leave` (the default) keeps them. Events that came from Google / Office 365 stay there either way.
- Deleting a calendar group instead is `delete-calendar-group` (its calendars and events stay).

**Output**

- The first call: `NOT DELETED: this needs confirmation.`, `It would permanently delete calendar 2551 "Install Crew 2" (its 12 events move to calendar "Outside Sales"; record appointments follow) in account 1668.` (or `and its 12 events (record appointments on them are cleared)`), then `--confirm DEL-XXXXXXXX`.
- After confirming: `Deleted calendar 2551 "Install Crew 2"; moved 12 events to "Outside Sales"`.

**Common errors**

- `<user> can't delete calendar "X" — only the calendar's owner or an Admin / Calendar Admin can manage calendar "X", as in the web app.`
- `<user> can't move calendar "X"'s events to calendar "Y" — they can only view calendar "Y" (no Edit permission), as in the web app.`
- `Events can't be moved to the calendar being deleted`; `--external-events must be delete or leave`.
- `Unknown calendar "X" (or one this user can't see). Calendars: …`.
- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: changing a flag changes the code; run it again without `--confirm`.

**Related:** `list-calendars`, `get-calendar`, `list-calendar-events --calendar-ids`, `delete-calendar-event`, `delete-calendar-group`, `share-calendar`.

Related commands: [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`clear-appointment`](clear-appointment.md), [`create-calendar`](create-calendar.md), [`update-calendar`](update-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`update-calendar-event`](update-calendar-event.md).
