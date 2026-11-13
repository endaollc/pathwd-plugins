# delete-calendar-event

Runs with: path_delete_calendar_items(command: "delete-calendar-event", args: "<flags>") (remove).

```
Usage: delete-calendar-event [options] <id>

Delete a calendar event (clears a record appointment field). Asks for
confirmation (--confirm <code>)

Arguments:
  id          Calendar event id

Options:
```

## Examples

- args: `6aa08c047f73f73dd53720c7`

## Notes

Deletes a calendar event permanently. When the event is a record's appointment, the record's appointment field is cleared too (it becomes Not Set). Deleting can't be undone; the user confirms first.

**Examples**

```
delete-calendar-event 6aa08c047f73f73dd53720c7
delete-calendar-event 6aa08c047f73f73dd53720c7 --confirm DEL-3F9A1C07
```

- Two calls. The first deletes nothing: it prints what would be deleted (the event, its time, its calendar and, for an appointment, the field and record it clears) and a code. After the user explicitly says yes, the same command with `--confirm <code>` deletes. The code works only for that exact command and expires within 20 minutes.
- The id is the event's 24-character id (`list-calendar-events`, or `list-calendar-events --record-id <id>` for a record's appointments).
- To clear a record's appointment by record and field instead: `clear-appointment <record id> --field <field>`. To keep the event but take it off the record: `update-calendar-event <id> --unlink`.
- Who may delete: the same people who may change it (Edit on its calendar; for an appointment, Edit on the record's business object). Read-only Google / Office 365 events can't be deleted from PATH.

**Output**

- The first call: `NOT DELETED: this needs confirmation.`, `It would permanently delete calendar event 6aa08c047f73f73dd53720c7 "Showroom walkthrough" (Fri, Nov 20, 2026, 9:00 AM–10:00 AM (America/Chicago)) on calendar 1729 "JC Merge 2" in account 1668.`, then `--confirm DEL-XXXXXXXX`.
- After confirming: `Deleted calendar event 6aa08c047f73f73dd53720c7`.

**Common errors**

- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: run the command again without `--confirm` for a fresh code.
- `<user> can't delete calendar event … — it belongs to an external (Google / Office 365) calendar and is read-only in PATH, as in the web app.`
- `<user> can't delete calendar event … — they can only view calendar "X" (no Edit permission), as in the web app.`
- `ERROR [404]: Calendar event not found`.

**Related:** `clear-appointment`, `update-calendar-event --unlink`, `get-calendar-event`, `delete-calendar` (a whole calendar).

Related commands: [`delete-calendar`](delete-calendar.md), [`delete-calendar-group`](delete-calendar-group.md), [`clear-appointment`](clear-appointment.md), [`create-calendar`](create-calendar.md), [`update-calendar`](update-calendar.md), [`share-calendar`](share-calendar.md), [`create-calendar-group`](create-calendar-group.md), [`update-calendar-group`](update-calendar-group.md), [`share-calendar-group`](share-calendar-group.md), [`create-calendar-event`](create-calendar-event.md), [`update-calendar-event`](update-calendar-event.md).
