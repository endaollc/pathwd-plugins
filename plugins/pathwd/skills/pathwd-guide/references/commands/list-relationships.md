# list-relationships

Runs with: path_data_model(command: "list-relationships", args: "<flags>") (read-only).

```
Usage: list-relationships [options]

List a business object's relationships (id, name, the business object it links
to), then each one's context

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
  --page <n>        Fetch only this single page (default: all relationships)
  --per-page <n>    Items per page (single-page mode) (default: "50")
  --all             Fetch all relationships (this is now the default)
```

## Notes

The relationships one business object holds: each is a link its records can carry to one record of another business object (a Job's "Job Main Customer" → Customers, a Job Item's "JI Job" → Jobs). These are the ids `link-record` and `unlink-record` take.

**Examples**

```
list-relationships --bo-name Job
list-relationships --bo-name "Job Item"
list-relationships --bo-id 5063
```

- **Only the relationships this business object owns are listed** (its records hold the link). Links pointing *at* it from elsewhere aren't: a Job's "Job Items" tab is the Job Item's "JI Job" relationship, found with `list-relationships --bo-name "Job Item"`. To see both sides for one record, use `list-related-records --record-id <id>` (`direct` and `inverse` rows).
- Several relationships can join the same two business objects (a Job has Job Address, Job Billing Address and Job Delivery Address, all → Addresses), and a business object can link to itself ("Linked Jobs", Job → Job). Pick by name and context.
- All relationships come back in one call; `--page` / `--per-page` fetch one page instead (50 a page). `--all` is the default and changes nothing.
- A relationship hidden from the acting user isn't listed.

**Output**

```
id    | name                 | to
------|----------------------|----------------
16483 | Linked Jobs          | Job (5050)
16474 | Job Delivery Address | Address (5044)
16480 | Job Main Customer    | Customer (5047)
[20 relationships]

Relationship context:
  Job Main Customer (16480): The customer the job is billed to.
```

- `id`: `link-record --relationship-id`. The listed business object holds the link (`link-record --record-id` is one of its records).
- `to`: the business object linked to, with its id (`link-record --related-record-id` is one of its records).
- `Relationship context:` under the table lists each relationship's designer description, when one has it (once per session, like all context).

**Common errors**

- `Provide either --bo-id or --bo-name`.
- `Business object "X" not found — did you mean "…"?`: `list-business-objects` lists the names.
- `<user> can't read business object 5063 — they don't have View permission on Job Items (business object 5063), as in the web app.`

**Related:** `list-related-records` (a record's actual links, both directions), `link-record`, `unlink-record`, `find-records --catalog` (relationship paths a formula can use), path-concepts' [relationships.md](../concepts/relationships.md).

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`get-field`](get-field.md), [`list-field-groups`](list-field-groups.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
