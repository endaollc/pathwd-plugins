# list-fields

Runs with: path_data_model(command: "list-fields", args: "<flags>") (read-only).

```
Usage: list-fields [options]

List a business object's fields: id, name, type, whether a value is mandatory
(req), the relationship a linked field comes from, and selection options; then
each field's context (what it holds and when to fill it). get-field shows one
field whole.

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
  --page <n>        Fetch only this single page (default: all fields)
  --per-page <n>    Items per page (single-page mode) (default: "100")
  --all             Fetch all fields (this is now the default)
```

## Examples

- args: `--bo-name Job`

## Notes

The fields of one business object: id, name, type, whether required, the relationship a linked field comes from, and each Selection's options. It is the list to read before `create-record` / `update-record`: `update-record --data` takes numeric field ids and stored values.

**Examples**

```
list-fields --bo-name Job
list-fields --bo-name "Job Item"
list-fields --bo-id 5050 --fields id,name,options
```

- All fields come back in one call; `--page` fetches one page instead (100 a page, `--per-page` to change it), ending `[100 of 224 fields — --page 2 for more]`; `--all` is the default and changes nothing.
- Only fields the record viewer can show the acting user are listed: a hidden field, a field in a hidden field group or on a hidden relationship isn't. A field whose visibility rule depends on the record is listed; `get-record` shows what one record shows. (The PATH Designer CLI lists every field and flags hidden ones.)
- **For searching, use `find-records --bo-name <bo> --catalog`**: it lists the names a formula can use (`[Department]`), each Selection's options, the relationship paths (`[Job Main Customer.Customer Name]`), milestones and workflow steps. `list-fields` is for ids, types and stored values.

**Output**

```
id     | name                     | type                  | req | from        | options
-------|--------------------------|-----------------------|-----|-------------|-------------------------------------------------
191635 | Est Ship Date            | date_time (date only)
191637 | Tax Exempt               | selection             |     |             | No, Yes
191629 | Department               | selection             | yes |             | Trade, Retail, Commercial
191672 | Deposit Instructions     | selection             |     |             | Customer will call with credit card payment, Deal…
191791 | Primary Vendor           | selection             |     |             | values of Vendor: Identifier
191650 | Deposit %                | number (%)
193309 | Job Address Identifier   | manual_format         |     | Job Address
[174 fields]
from: the field shows a linked record's value (that relationship); change it on that record, or change the link (link-record / unlink-record).
Options cut short with "…": get-field --field-id <id> lists them all.
Format, default value, group and context of one field: get-field --field-id <id>.

Field context:
  Pallet Warning (286701): Shown on the scan screen when a pallet …
```

Columns to read:

- `id`: the `field_id` in `create-record` / `update-record --data`.
- `type`: the `field_type`; what each takes as a stored value is in the [`path-cli` guide](../commands.md)'s [field-values.md](../cli/field-values.md). `(date only)`: the field stores noon on the user's clock that day. `(%)`: a number shown as a percent, stored as a fraction (50% = `0.5`).
- `req`: `yes` when the field is required.
- `from`: the relationship a linked field shows a value through; nothing writes it on this record.
- `options`: a preset Selection's exact option texts (the stored value is one of them), cut at 50 characters; `values of <business object>: <field>` for a Selection drawn from another business object's records.
- Under the table, `Field context:` lists the designer's description of each field that has one (what it holds and when to fill it); long ones are cut and `get-field` prints them whole. Like all context, it is shown once per session.
- Computed types (`calculation`, `manual_format`, `time_span_expression`, `aggregate_number`, `aggregate_text`, `aggregate_count`, `auto_number`) are filled by PATH; nothing writes them. A `notes` field takes `add-record-note`, an `appointment` field `set-appointment`.

**Common errors**

- `Provide either --bo-id or --bo-name`.
- `Business object "X" not found — did you mean "…"?` / `Ambiguous business object name …`: `list-business-objects` lists the names.
- `<user> can't read business object 5063 — they don't have View permission on Job Items (business object 5063), as in the web app.`: outside the Designer, only business objects the user may view can be read.

**Related:** `get-field` (one field), `list-field-groups`, `find-records --catalog`, `create-record`, `update-record`, `list-appointment-fields`.

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`get-field`](get-field.md), [`list-field-groups`](list-field-groups.md), [`list-relationships`](list-relationships.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
