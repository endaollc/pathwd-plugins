# list-appointment-fields

Runs with: path_data_model(command: "list-appointment-fields", args: "<flags>") (read-only).

```
Usage: list-appointment-fields [options]

List the appointment fields calendar events can be scheduled on

Options:
  --bo-id <id>      Only this business object
  --bo-name <name>  Only this business object (by name)
```

## Notes

The appointment fields calendar events can be scheduled on, as the web's new-appointment form lists them: each is a field on a business object (a Job's "Soft Schedule Appointment", an Appointment's "Scheduled Date and Time") whose value is a calendar event.

**Examples**

```
list-appointment-fields
list-appointment-fields --bo-name Job
set-appointment 6a9056455ff33756afba5084 --field "Soft Schedule Appointment" --calendar "Outside Sales" --start "2026-12-03 09:00" --end "2026-12-03 11:00"
```

- Without a flag: every business object's appointment fields; `--bo-name` / `--bo-id` keep one business object's.
- Only a business object's own appointment fields are listed, not relationship fields that show a linked record's appointment.
- An appointment field isn't written with `update-record`: `set-appointment <record id> --field <id or name>` schedules or moves it (a first scheduling also needs `set-appointment --start` and `set-appointment --calendar`), `clear-appointment` removes it (after a confirmation), and `create-calendar-event --record <id>` creates one from the calendar side (its `create-calendar-event --field` can be left out when the record has only one appointment field).

**Output**

```
id     | name                      | bo
-------|---------------------------|-------------------------
193216 | Scheduled Date and Time   | Internal Bulletin (5094)
191717 | Soft Schedule Appointment | Job (5050)
[4 appointment fields]
```

- `id` is what `set-appointment --field` takes (the name works too); `bo` is the business object with its id.
- `[0 appointment fields]` for a business object means it has no appointment field: its records can't be scheduled directly, though a linked record (an Appointment linked to the Job) may be.

**Common errors**

- `Business object "X" not found — did you mean "…"?`: `list-business-objects` lists the names.

**Related:** `set-appointment`, `clear-appointment`, `create-calendar-event`, `list-calendars`, `list-calendar-events`, `get-calendar-event`, `list-fields` (appointment fields have type `appointment`).

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`get-field`](get-field.md), [`list-field-groups`](list-field-groups.md), [`list-relationships`](list-relationships.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
