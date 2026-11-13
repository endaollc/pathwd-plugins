# set-appointment

Runs with: path_update_record(command: "set-appointment", args: "<flags>") (change).

```
Usage: set-appointment [options] <record-id>

Schedule or change a record's appointment field (creates or updates the
calendar event that owns it)

Arguments:
  record-id             Record id

Options:
  --field <id|name>     Appointment field on the record
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
```

## Examples

- args: `6a9056455ff33756afba5084 --field "Install Date" --start 2026-11-20T09:00 --end 2026-11-20T12:00`

## Notes

Schedules or changes a record's appointment field, as the record's Appointments tab does. The field's value is a copy of a calendar event, so this creates that event or updates the one that holds it; `update-record` never writes an appointment field. Only the flags given change.

**Examples**

```
set-appointment 6a974f7139848dc849b4a720 --field "Scheduled Date and Time" --start "2026-11-19 10:00" --calendar "Outside Sales"
set-appointment 6a974f7139848dc849b4a720 --field "Scheduled Date and Time" --start "2026-11-20 13:30"
set-appointment 6a974f2da374375d4c083653 --field "Soft Schedule Appointment" --all-day --start 2026-12-01 --end 2026-12-03
set-appointment 6a974f7139848dc849b4a720 --field 191972 --location "1200 Example Ln, Austin, TX" --description "Gate code 4411"
```

- The record id (24 characters) comes first; `--field` (id or name) is the record's own appointment field. `list-appointment-fields` lists them for every business object (e.g. Job's "Soft Schedule Appointment", Appointment's "Scheduled Date and Time").
- Not scheduled yet: `--start` and `--calendar` are needed; the title comes from the field's Event Description, as in the web app, unless `--title`. Already scheduled: give only what changes; a moved `--start` keeps the length.
- Times are read on the signed-in user's clock; a start alone lasts an hour. `--all-day` dates are whole days on the account's calendar; multi day follows the dates unless `--multi-day` or `--no-multi-day` is given. `--no-all-day` gives 8–9am on the same days.
- The user needs Edit on the record's business object, the field in their record viewer, and Edit on the calendar.
- A field that mirrors a linked record's appointment is changed on that record.
- To clear it: `clear-appointment`.

**Output:** one line: `Created event …` (a new appointment) or `Updated event …` with the event id, title, calendar, time on the user's clock and `; Scheduled Date and Time (field 191972) on Appointments record <id>`. `get-record` shows the field's new value.

**Common errors**

- `Scheduled Date and Time isn't scheduled yet — give --start and --calendar`; `--calendar is required to schedule a new appointment`.
- `No field "X" on this business object. Appointment fields: 191972 Scheduled Date and Time`; `X is not an appointment field…`; `X shows a linked record's appointment — change it on that record…`.
- `Nothing to change — give --start, --calendar, --title, … (see --help)`.
- `<user> can't change Scheduled Date and Time on record … — they can only view calendar "X" (no Edit permission), as in the web app.` (or the business object / record viewer reason).
- `end must be after start`; `end is on a later day than start — set multi day to yes, or leave it off`; `Can't read "…" as a date…`.

**Related:** `clear-appointment`, `list-appointment-fields`, `list-calendar-events --record-id`, `create-calendar-event --record`, `list-calendars`.

Related commands: [`update-record`](update-record.md), [`unarchive-record`](unarchive-record.md), [`unarchive-record-file`](unarchive-record-file.md), [`update-record-note`](update-record-note.md), [`update-record-file`](update-record-file.md), [`update-message`](update-message.md).
