# update-calendar-event

Runs with: path_manage_calendars(command: "update-calendar-event", args: "<flags>") (change).

```
Usage: update-calendar-event [options] <id>

Update a calendar event, or link/unlink it to a record appointment field

Arguments:
  id                    Calendar event id

Options:
  --title <text>        Event title
  --start <date>        Start: "YYYY-MM-DD HH:mm", YYYY-MM-DD, an ISO
                        timestamp, today, tomorrow, +3d (your time zone)
  --end <date>          End (same formats). Default: a moved start keeps the
                        length, a new event lasts an hour
  --calendar <id|name>  Calendar
  --all-day             All day: the dates are whole days on the account
                        calendar
  --no-all-day          Timed (turning all day off gives 8–9am on the same
                        days)
  --multi-day           Spans several days (default: follows the dates)
  --no-multi-day        One day (brings the end back to the start day)
  --color <color>       Event color, e.g. #c33 ("" for the calendar color)
  --location <text>     Location
  --description <text>  Description
  --record <id>         Attach the event to this record's appointment field
  --field <id|name>     The appointment field (default: the record has only
                        one)
  --unlink              Detach the event from its record (clears the record's
                        appointment field)
  --data <json>         Raw JSON body, passed through as-is (start_date /
                        end_date as ISO timestamps, UTC); flags override its
                        keys
```

## Examples

- args: `6aa08c047f73f73dd53720c7 --start 2026-11-21T09:00 --end 2026-11-21T10:00`

## Notes

Changes a calendar event, as editing it on the web calendar does, or attaches it to / detaches it from a record's appointment field. Only the flags given change. Works on standalone events, record appointments (the record's field follows the event) and writable events from merged Google / Office 365 calendars.

**Examples**

```
update-calendar-event 6aa08c047f73f73dd53720c7 --start "2026-11-21 09:00"
update-calendar-event 6aa08c047f73f73dd53720c7 --title "Showroom walkthrough (rescheduled)" --calendar "Service Lead 1"
update-calendar-event 6a907ae05ff33756afbac28f --all-day --start 2026-12-04 --end 2026-12-05
update-calendar-event 6aa08c047f73f73dd53720c7 --record 6a974f7139848dc849b4a720 --field "Scheduled Date and Time"
update-calendar-event 6a986d9a67f01e74e7ea15f4 --unlink
```

- The id is the event's 24-character id (`list-calendar-events`).
- A moved `--start` keeps the event's length; `--end` sets it. Times are on the signed-in user's clock; all-day dates are whole days on the account's calendar. `--no-all-day` makes an all-day event 8–9am on the same days; `--no-multi-day` brings the end back to the start's day.
- `--calendar` (id or name) moves it; the user needs Edit on both calendars.
- `--record` (24-character id) with `--field` makes the event that record's appointment; a record it filled before has that field cleared. A field that already has its own event can't take a second one (`clear-appointment` it first). `--unlink` detaches the event: it stays, standalone, and the record's field is cleared.
- `--color ""` goes back to the calendar's color; `--location ""` and `--description ""` clear those.
- For a record's appointment, `set-appointment <record id> --field …` does the same by record and field, without looking up the event.

**Output:** one line: `Updated event 6a986d9a67f01e74e7ea15f4 "Retail Sales Visit : 26-0074" on Outside Sales (1626): 2026-11-18 10:00–11:00 CST`, and `; <field> (field <id>) on <business object> record <id>` when it is a record appointment. Times on the user's clock.

**Common errors**

- `Nothing to change — give a flag (see --help) or --data`; `Use either --record (link) or --unlink, not both`.
- `<user> can't change calendar event … — it belongs to an external (Google / Office 365) calendar and is read-only in PATH, as in the web app.`
- `<user> can't change calendar event … — they can only view calendar "X" (no Edit permission), as in the web app.`; `… they don't have Edit permission on <business object>…` for a record appointment.
- `end must be after start`; `end is on a later day than start — set multi day to yes, or leave it off`.
- `A calendar event already exists for this appointment field (or provider event)`: the field given with `--record` is already scheduled.
- `ERROR [404]: Calendar event not found`: a wrong or deleted id.

**Related:** `get-calendar-event`, `set-appointment`, `create-calendar-event`, `delete-calendar-event`, `list-calendar-events`.

Related commands: [`create-calendar`](create-calendar.md), [`update-calendar`](update-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`clear-appointment`](clear-appointment.md).
