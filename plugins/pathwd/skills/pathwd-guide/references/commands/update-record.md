# update-record

Runs with: path_update_record(command: "update-record", args: "<flags>") (change).

```
Usage: update-record [options]

Update a record by ID

Options:
  --record-id <id>  Record ID
  --data <json>     JSON body with fields to update
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --data '{"fields": [{"field_id": 191620, "value": "Retail"}]}'`

## Notes

Changes field values on a record, as an edit in the record viewer does (it shows in the record's history). Values are stored values, sent as given.

**Examples**

```
update-record --record-id 6a9056455ff33756afba5084 --data '{"fields":[{"field_id":191620,"value":"Trade"}]}'
update-record --record-id 6a9056455ff33756afba5084 --data '{"fields":[{"field_id":191635,"value":"2026-10-15T17:00:00.000Z"},{"field_id":191650,"value":0.25}]}'
update-record --record-id 6a9056455ff33756afba5084 --data '{"fields":[{"field_id":191635,"value":null}]}'
```

- Only the fields listed change; `null` clears a number, date, checkbox or selection, `""` clears text.
- The forms per type (Percent `0.25` = 25%, date-only at noon on the user's clock, seconds, phone digits): the [`path-cli` guide](../commands.md)'s [field-values.md](../cli/field-values.md).
- Not for: Notes fields (`add-record-note`), Appointment fields (`set-appointment`), links (`link-record`), files, calculated fields, milestones.

**Output**

```
Updated record 6a9056455ff33756afba5084 (Job 26-0050): Department, Deposit %
Milestones: Ordering, Deposit Needed
```

- Lines to read: the record and the fields sent, then the milestones it is in after the save (calculated fields and milestones update with it). `get-record --record-id <id>` shows the new values.

**Common errors**

- `<user> can't edit record … — … isn't visible to them in the record viewer`: the field isn't one their record viewer shows on this record, or they lack Edit.
- A Notes field is refused, naming `add-record-note`.
- A wrong form isn't refused (`8.25` in a Percent field saves 825%): read the value back with `get-record`.

**Bulk changes:** one `update-record` per record; find the records with `find-records` first and confirm the list with the user. A pathway that makes the change is usually better.

**Related:** `get-record --raw` (field ids with their current stored values), `list-fields` (ids, types, options), `pathway-run available --record-id <id>`.

Related commands: [`unarchive-record`](unarchive-record.md), [`unarchive-record-file`](unarchive-record-file.md), [`set-appointment`](set-appointment.md), [`update-record-note`](update-record-note.md), [`update-record-file`](update-record-file.md), [`update-message`](update-message.md).
