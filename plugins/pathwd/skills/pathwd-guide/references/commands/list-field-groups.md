# list-field-groups

Runs with: path_data_model(command: "list-field-groups", args: "<flags>") (read-only).

```
Usage: list-field-groups [options]

List a business object's field groups (id, name), then each one's context

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
  --all             Read the account-wide field group list (fresh) instead of
                    the business object's
```

## Notes

The field groups of one business object: the titled sections (Details, Deposit Information, Follow Ups) the record viewer arranges its fields in.

**Examples**

```
list-field-groups --bo-name Job
list-field-groups --bo-id 5050
```

- `get-field` names a field's group; `get-record` shows one record's fields under their groups, as the record viewer does.
- Only groups the acting user may see are listed.
- `--all` reads the groups from the full account list instead of the business object's own list; both give the same groups, so it is rarely needed.

**Output**

```
id    | name
------|---------------------
14608 | System
14609 | Details
14712 | Deposit Information
[14 field groups]
```

- Columns: `id`, `name`. The "System" group holds the fields PATH keeps itself, such as Created Date and Modified Date.
- `Field group context:` under the table lists each group's designer description, when one has it (once per session, like all context).

**Common errors**

- `Provide either --bo-id or --bo-name`.
- `Business object "X" not found — did you mean "…"?`: `list-business-objects` lists the names.
- `<user> can't read business object 5050 — they don't have View permission on Jobs (business object 5050), as in the web app.`

**Related:** `list-fields`, `get-field`, `get-record` (the fields one record shows).

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`get-field`](get-field.md), [`list-relationships`](list-relationships.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
