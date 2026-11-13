# get-field

Runs with: path_data_model(command: "get-field", args: "<flags>") (read-only).

```
Usage: get-field [options]

Show one field: type, whether a value is mandatory, whether --data can write
it, group, the relationship a linked field comes from, format, default, every
selection option, and its context (the designer's description of what the field
holds and when to fill it)

Options:
  --bo-id <id>         Business object ID
  --bo-name <name>     Business object name
  --field-id <id>      Field ID
  --field-name <name>  Field name (case-insensitive)
```

## Notes

One field in full: business object, type, whether required and writable, group, the relationship a linked field comes from, display format, default value, every Selection option, and the field's context (what it holds and when to fill it). Fetched by id or by name.

**Examples**

```
get-field --field-id 191637
get-field --bo-name Job --field-name "tax exempt"
get-field --bo-name "Job Item" --field-name "Est Ship Date"
```

- `--field-id` alone is enough. `--field-name` needs `--bo-name` or `--bo-id`, matches the whole name ignoring case (never part of it), and picks the field on that business object only. A field reached through a relationship (`[Job Main Customer.Customer Name]` in a formula) lives on the other business object: ask for it there (`--bo-name Customer --field-name "Customer Name"`).
- A field's context is the designer's description of what it holds and when to fill it; it is often the best answer to "what does this field mean?". It is printed whole here (lists cut it).

**Output**

```
id: 191688
name: Lead Source
business object: Job (5050)
type: selection
required: yes
writable: yes (create-record / update-record --data)
group: Details (14609)
options:
  Referral By Client
  Manufacturer Lead
  Website
blank option: yes
context: Where the lead first came from; set when the job is created.
```

Lines to read (a line with nothing to say is left out):

- `type`: the `field_type` ([field-values.md](../cli/field-values.md) in the [`path-cli` guide](../commands.md) gives each one's stored form); `(date only)` / `(%)` as in `list-fields`.
- `writable`: whether `create-record` / `update-record --data` can set it (`no` for computed, linked, system and Notes fields).
- `from`: a linked field's relationship and the field it shows on the linked record. `counts`: what an aggregate totals.
- `format`: a date's display format (`M/d/yyyy (date only: stored as noon on the user's clock)`) or a number's (`$, 2 decimals, commas`; `%` means stored as a fraction).
- `options`: every preset option's exact text; `values from`: the business object, field and folder a records-backed Selection draws on. `blank option`: whether it can be left blank.
- `standard field`: PATH's own fields (`CreatedDate`, `Identifier`…). `searchable`: quick search / advanced search.
- `context`: the designer's text, whole (`(none)` when there is none).

**Common errors**

- `Field "Tax Exemptoin" not found on Jobs — did you mean "Tax Exemption"?`: no field has that whole name; up to three close names follow, or, with nothing close, `(list-fields --bo-id 5050 lists them)`.
- `--field-name requires --bo-id or --bo-name to scope the lookup.`
- `Provide --field-id or --field-name`.
- `Field 191352 not found`: no field with that id.
- `<user> can't read field 191637 — they don't have View permission on Jobs (business object 5050), as in the web app.`: outside the Designer, only fields of business objects the user may view can be read.

**Related:** `list-fields`, `list-field-groups`, `find-records --catalog` (the field names a formula can use), `list-appointment-fields`.

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`list-field-groups`](list-field-groups.md), [`list-relationships`](list-relationships.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
