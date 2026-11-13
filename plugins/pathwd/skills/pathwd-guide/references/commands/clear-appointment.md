# clear-appointment

Runs with: path_delete_calendar_items(command: "clear-appointment", args: "<flags>") (remove).

```
Usage: clear-appointment [options] <record-id>

Clear a record's appointment field (deletes the calendar event that owns it).
Asks for confirmation (--confirm <code>)

Arguments:
  record-id          Record id

Options:
  --field <id|name>  Appointment field on the record
```

## Examples

- args: `6a9056455ff33756afba5084 --field "Install Date"`

## Notes

Clears a record's appointment field (it becomes Not Set), as the Appointments tab's Clear does: the calendar event that holds the field is deleted. Deleting can't be undone; the user confirms first.

**Examples**

```
clear-appointment 6a974f7139848dc849b4a720 --field "Scheduled Date and Time"
clear-appointment 6a974f7139848dc849b4a720 --field "Scheduled Date and Time" --confirm DEL-8C21E4B0
```

- Two calls. The first deletes nothing: it prints the event that would be deleted (title, time, calendar), the field and record, and a code. After the user explicitly says yes, the same command with `--confirm <code>` clears it. The code works only for that exact command and expires within 20 minutes.
- A field that isn't scheduled needs no confirmation: the command prints `<field> is already not set` and changes nothing.
- The record id (24 characters) comes first; `--field` takes the field's id or name (`list-appointment-fields`).
- To keep the event on the calendar and only take it off the record: `update-calendar-event <event id> --unlink`. To reschedule instead: `set-appointment`.
- The user needs Edit on the record's business object, the field in their record viewer, and Edit on the event's calendar; a read-only Google / Office 365 event can't be deleted from PATH.

**Output**

- The first call: `NOT DELETED: this needs confirmation.`, `It would permanently delete calendar event … "Retail Sales Visit …" (…) on calendar 2085 "…", which holds Scheduled Date and Time on … (the field becomes Not Set) in account 1668.`, then `--confirm DEL-XXXXXXXX`.
- After confirming: `Cleared Scheduled Date and Time (was Wed, Sep 2, 2026 8:00 AM – Thu, Sep 3, 2026 10:45 AM (America/Chicago); deleted calendar event 6a986d9a67f01e74e7ea15f4)`.

**Common errors**

- `No field "X" on this business object. Appointment fields: …`; `X is not an appointment field…`; `X shows a linked record's appointment — change it on that record…`.
- `<user> can't clear Scheduled Date and Time on record … — they don't have Edit permission on Appointments (business object 5055), as in the web app.` (or the calendar or record viewer reason).
- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: run it again without `--confirm` for a fresh code.

**Related:** `set-appointment`, `delete-calendar-event`, `update-calendar-event --unlink`, `list-calendar-events --record-id`.

Related commands: [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`delete-calendar-event`](delete-calendar-event.md), [`create-calendar`](create-calendar.md), [`update-calendar`](update-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`update-calendar-event`](update-calendar-event.md).
