# create-calendar-event

Runs with: path_manage_calendars(command: "create-calendar-event", args: "<flags>") (change).

Also spelled: create-appointment.

```
Usage: create-calendar-event|create-appointment [options]

Create a calendar event (standalone, or a record appointment with
--record/--field)

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
  --record <id>         Schedule it on this record's appointment field
  --field <id|name>     The appointment field (default: the record has only
                        one)
  --data <json>         Raw JSON body for the event, passed through as-is
                        (start_date / end_date as ISO timestamps, UTC); flags
                        override its keys
```

## Examples

- args: `--title "Site visit" --start 2026-11-20T09:00 --end 2026-11-20T10:00 --calendar Installs`

## Notes

Creates a calendar event, as the web calendar's new-event form does: a standalone event, or, with `--record`, a record's appointment (the event fills the record's appointment field). Also spelled `create-appointment`.

**Examples**

```
create-calendar-event --title "Showroom walkthrough" --start "2026-11-20 09:00" --calendar "Outside Sales"
create-calendar-event --title "Warehouse inventory" --start 2026-11-23 --end 2026-11-24 --all-day --calendar 1626
create-calendar-event --title "Crew training" --start "2026-11-18 13:00" --end "2026-11-18 16:30" --calendar "Service Lead 1" --location "4820 Industrial Oaks Blvd" --color "#1565C0"
create-calendar-event --record 6a974f7139848dc849b4a720 --start "2026-11-19 10:00" --calendar "Outside Sales"
```

- `--title`, `--start` and `--calendar` are needed (with `--record`, the title defaults to the appointment field's Event Description, as in the web app).
- Times are read on the signed-in user's clock: `"2026-11-20 09:00"`, an ISO timestamp, `today`, `tomorrow`, `+3d`. A start alone lasts an hour.
- `--all-day`: the dates are whole days on the account's calendar (`--start 2026-11-23 --end 2026-11-24` covers both days). Multi day follows the dates unless `--multi-day` or `--no-multi-day` is given.
- `--calendar` takes an id or name; the user needs Edit on it (shared with edit, its owner, "All Users" edit, or an Admin / Calendar Admin).
- `--record` (24-character record id) with `--field` (id or name; not needed when the record's business object has one appointment field) makes it that field's appointment; the user also needs Edit on the record's business object. `set-appointment` does the same and also moves an existing one. An appointment field is never written with `update-record`.
- `--color` is a hex such as `#c33`; leave it out for the calendar's color.

**Output:** one line: `Created event 6ae3873c1a07b3f61f062196 "Delivery : 26-0057" on Delivery Truck 1 (1623): 2026-11-14 08:00–09:00 CST; Scheduled Date and Time (field 191972) on Appointments record 6a95b56babc978b6acadc059`. The event id (24 characters) is what `get-calendar-event`, `update-calendar-event` and `delete-calendar-event` take; the time is on the user's clock (an all-day event: `2026-11-14 all day`); the part after `;` is there for a record appointment. `--quiet` prints the id alone.

**Common errors**

- `Missing title, start_date, calendar_id — give --title, --start and --calendar (or the keys in --data)`.
- `Unknown calendar "X". Calendars: 1624 Service Lead 1, …`.
- `<user> can't create an event on calendar 1626 — they can only view calendar "Outside Sales" (no Edit permission), as in the web app.`
- `The record has several appointment fields — pick one with --field: …`; `The record's business object has no appointment fields`; `<field> shows a linked record's appointment — change it on that record…`.
- `A calendar event already exists for this appointment field (or provider event)`: the field is already scheduled; move it with `set-appointment`.
- `end must be after start`; `end is on a later day than start — set multi day to yes, or leave it off`; `Can't read "…" as a date…`.

**Related:** `list-calendars`, `list-appointment-fields`, `set-appointment`, `update-calendar-event`, `list-calendar-events`.

Related commands: [`create-calendar`](create-calendar.md), [`update-calendar`](update-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`update-calendar-event`](update-calendar-event.md), [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`clear-appointment`](clear-appointment.md).
