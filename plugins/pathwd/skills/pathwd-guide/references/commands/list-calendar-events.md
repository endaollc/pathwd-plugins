# list-calendar-events

Runs with: path_calendars(command: "list-calendar-events", args: "<flags>") (read-only).

Also spelled: list-appointments.

```
Usage: list-calendar-events|list-appointments [options]

List calendar events (all origins) in a date window: id, when (your clock;
all-day events as days), calendar, from (the record id of a record appointment,
or the provider of a synced Google / Office 365 event; read-only), title.
get-calendar-event <id> shows one in full

Options:
  --start-date <date>   Window start: YYYY-MM-DD (from the start of that day),
                        "YYYY-MM-DD HH:mm", today, tomorrow, +3d — your time
                        zone; an ISO timestamp with Z/offset as-is
  --end-date <date>     Window end (same formats; a bare day runs to the end of
                        that day, inclusive)
  --calendar-ids <ids>  Comma-separated calendar ids to include
  --record-id <id>      Only events scheduled on this record
  --include-related     With --record-id: also the appointments of records it
                        links to and records linking to it (the record's
                        Appointments tab)
  --sources <list>      Only these origins, comma-separated: standalone,
                        record_appointment, provider
  --search <text>       Search events by text instead of listing a window
  --page <n>            Page number (search only) (default: "1")
  --per-page <n>        Items per page (search only) (default: "25")
```

## Examples

- args: `--start-date 2026-11-09 --end-date 2026-11-15`

## Notes

Lists calendar events in a date window, of every origin: standalone events, records' appointments (an event that fills a record's appointment field) and events from merged Google / Office 365 calendars. Also spelled `list-appointments`.

**Examples**

```
list-calendar-events --start-date 2026-11-16 --end-date 2026-11-20
list-calendar-events --start-date today --end-date today --calendar-ids 1626,1887
list-calendar-events --start-date 2026-11-01 --end-date 2026-11-30 --sources record_appointment
list-calendar-events --record-id 6a974f7139848dc849b4a720 --include-related
list-calendar-events --search "Retail Sales Visit" --per-page 50
```

- Dates are read on the signed-in user's clock: a bare day (`2026-11-16`, `today`, `tomorrow`, `+3d`) runs from the start of that day for `--start-date` to its end, inclusive, for `--end-date`; `"2026-11-16 13:00"` is that time; an ISO timestamp with `Z` or an offset is used as-is. Words like "next week" aren't read: work out the dates.
- An event that overlaps the window is listed, sorted by start. Without dates, every event on the visible calendars comes back. The window list isn't paged; only `--search` pages (25 a page, `--page` 2, 3, …).
- `--calendar-ids` takes numeric ids (`list-calendars`); only calendars the user can view are ever searched.
- `--sources`: `standalone`, `record_appointment`, `provider` (comma-separated).
- `--record-id` lists the appointments on one record; `--include-related` adds those of records it links to and records linking to it (the record's Appointments tab).
- `--search` finds events by words in their text (title, location, …) instead of listing a window; `--start-date`, `--end-date`, `--calendar-ids` and `--sources` narrow it.

**Output**

```
id                       | when                                | calendar         | from                     | title
-------------------------|-------------------------------------|------------------|--------------------------|----------------------------------------
6a907a155ff33756afbac1da | 2026-08-31 08:30–09:30              | Outside Sales    |                          | Sales Team Monthly Sync
6a986d9a67f01e74e7ea15f4 | 2026-09-02 08:00 – 2026-09-03 10:45 | Shelley Test MS  | 6a974f7139848dc849b4a720 | Retail Sales Visit : 26-0074 - 405 E Loop St…
6a907ae05ff33756afbac28f | 2026-09-04 all day                  | Todd Demo        |                          | Todd's Day Off
6ad102794ec5aa8f7fcf0d9e | 2026-10-15 12:30–13:00              | JC Merge 2       | outlook                  | 2nd event
[74 events · times America/Chicago]
```

- Columns to read: `id` (the 24-character event id `get-calendar-event`, `update-calendar-event` and `delete-calendar-event` take), `when`, `calendar` (its name), `from`, `title`.
- `when`: timed events on the user's clock (the footer names the zone); all-day events as days on the account's calendar (`2026-09-04 all day`, `2026-09-04 – 2026-09-06 all day`); `(repeats)` on a recurring synced event, listed once at its first occurrence.
- `from`: the record id of a record appointment (an event that fills a record's appointment field; changing it changes the field); `google` / `outlook` for an event from a merged calendar; blank for a standalone event; `read-only` when it can't be changed or deleted from PATH.
- `--search` pages: `[25 of 60 events · times America/Chicago — --page 2 for more]`.
- Location, description, the appointment field and who created it: `get-calendar-event <id>`.

**Common errors**

- `Can't read "next week" as a date. Use YYYY-MM-DD, "YYYY-MM-DD HH:mm", an ISO timestamp, now, today, tomorrow or +3d (times are in America/Chicago).`
- `--include-related needs --record-id`; `--include-related lists a window; it does not combine with --search`.

**Related:** `get-calendar-event`, `list-calendars`, `create-calendar-event`, `set-appointment`, `list-appointment-fields`.

Related commands: [`list-calendars`](list-calendars.md), [`get-calendar`](get-calendar.md), [`list-calendar-groups`](list-calendar-groups.md), [`get-calendar-group`](get-calendar-group.md), [`get-calendar-event`](get-calendar-event.md).
