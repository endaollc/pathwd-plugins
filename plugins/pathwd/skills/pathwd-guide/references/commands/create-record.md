# create-record

Runs with: path_create_record(command: "create-record", args: "<flags>") (add).

```
Usage: create-record [options]

Create a record on a business object

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name (resolved to ID)
  --data <json>     JSON body with business_object_id (optional), fields, and
                    optional user_id
```

## Examples

- args: `--bo-name Customer --data '{"fields": [{"field_id": 20401, "value": "Acme Builders"}]}'`

## Notes

Creates a record on a business object as the acting user, with field values given as stored values. When a pathway creates this kind of record (a dashboard pathway, or one on the parent record), it does the related work too (links, tasks, milestones) and is usually the better route.

**Examples**

```
create-record --bo-name Customer --data '{"fields":[{"field_id":20401,"value":"Smith Plumbing"},{"field_id":20402,"value":"5125550126"}]}'
create-record --bo-name Job --data '{"fields":[{"field_id":191620,"value":"Retail"},{"field_id":191650,"value":0.1},{"field_id":191635,"value":"2026-10-15T17:00:00.000Z"}]}'
```

- `fields` is an array of `{"field_id": <id>, "value": <stored value>}`; the ids above are examples: each account's come from `list-fields --bo-name <bo>`, and an existing record's `get-record --record-id <id> --raw` shows each field's id with its stored value.
- Stored values: Percent as a fraction (`0.1` = 10%), dates as UTC ISO with date-only fields at noon on the user's clock, Time Span in seconds, Phone as digits, Selection as one option's exact text, numbers as numbers. Every type: the [`path-cli` guide](../commands.md)'s [field-values.md](../cli/field-values.md).
- Links to other records come after: `link-record --record-id <new id> --related-record-id <customer id> --relationship-id <id>`.

**Output**

```
Created record 6af4b704e1333c2cca9714ba (Customer Smith Plumbing): Customer Name, Phone
Milestones: New Customer
```

- Lines to read: the new record's id (for `link-record`, `get-record` and the other record commands), its business object and identifier, the fields set, and the milestones it entered. `get-record --record-id <id>` shows the whole record.

**Common errors**

- `<user> can't create a record on business object … — …`: no Edit on the business object, or a field the record viewer doesn't show them.
- `Provide either --bo-id or --bo-name`; `Business object "X" not found — did you mean "…"?` (singular or plural name, any case).
- A JSON error: `--data` must be valid JSON in single quotes; `fields` must be an array.
- Nothing refuses a wrong value form: `50` in a Percent field saves 5000%. Check with `get-record` after.

**Related:** `update-record`, `link-record`, `pathway-run available --from dashboard`, `list-fields`.

Related commands: [`link-record`](link-record.md), [`archive-record`](archive-record.md), [`unlink-record`](unlink-record.md).
