# list-workflow-milestones

Runs with: path_data_model(command: "list-workflow-milestones", args: "<flags>") (read-only).

```
Usage: list-workflow-milestones [options]

List a business object's workflow milestones (id, name, initial/disabled, the
workflow tasks it creates or offers, what runs on entry), then each one's
context. get-filter milestone <id> prints a milestone's entry criteria.

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
  --all             Read the account-wide milestone list (fresh) instead of the
                    business object's
```

## Examples

- args: `--bo-name Job`

## Notes

The milestones of one business object: the stages its records can be in (Lead, Full Quote Out, Ordering, Paid In Full). PATH computes a record's milestones from each milestone's criteria; nobody sets them by hand, and a record can be in several at once.

**Examples**

```
list-workflow-milestones --bo-name Job
list-workflow-milestones --bo-id 5050
get-filter milestone 17106
```

- The last line prints one milestone's criteria as a formula (`HASALLSTEPS("Full Quote Sent to Customer") AND NOTHASSTEP("Job Won") AND …`): which workflow steps and field values put a record in it. Milestone ids come from this list.
- Milestone names are what formulas use: `find-records --bo-name Job --logic 'INMILESTONE("Full Quote Out")'`. A name has to be one of this business object's milestones (`Unknown milestone "Won"` otherwise).
- `--all` reads the milestones from the full account list instead of the business object's own; both give the same milestones.

**Output**

```
id    | name             | flags   | creates | offers              | runs
------|------------------|---------|---------|---------------------|----------------------------------
17103 | Lead             |         |         | 5839 5844 5843 5850
17123 | Qualified        |         | 5845    | 5839 5843 5844
17107 | Lost
17111 | Ready to Order   |         | 5846    | 5838                | pathway Create Job In Accounting (25895)
[50 milestones]
creates / offers: workflow task ids (list-workflow-tasks) created when a record enters / offered while it is in; runs: the pathway or action run on entry. Entry criteria: get-filter milestone <id>.

Milestone context:
  Ready to Order (17111): Deposit is in and the order can be placed.
```

Columns to read, in the designer's order:

- `id` (`get-filter milestone <id>`), `name` (what formulas use).
- `flags`: `initial` (the default milestone, where a record sits when it meets no other's criteria), `disabled` (turned off by the designer).
- `creates`: workflow task ids created when a record enters; `offers`: task ids offered while the record is in it (titles in `list-workflow-tasks`).
- `runs`: a pathway run when a record enters (`(once)`: only the first time), or `action <id>` fired on entry.
- `Milestone context:` under the table: what each stage means, when the designer wrote it (once per session, like all context).

**Why is a record (not) in a milestone?** Compare `get-filter milestone <id>` with the record's steps and values (`get-record --record-id <id> --raw` lists its `steps` and `milestones` by id); the [`path-milestone-explain` guide](../../../pathwd-history/references/milestone-explain.md) walks it rule by rule. Workflow concepts: path-concepts' [workflow.md](../concepts/workflow.md).

**Common errors**

- `Provide either --bo-id or --bo-name`.
- `Business object "X" not found — did you mean "…"?`: `list-business-objects` lists the names.
- `<user> can't read business object 5050 — they don't have View permission on Jobs (business object 5050), as in the web app.`

**Related:** `list-workflow-steps`, `list-workflow-tasks`, `get-filter milestone`, `find-records` (`INMILESTONE`, `MILESTONEFIRSTENTERED`), `get-record` (a record's current milestones).

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`get-field`](get-field.md), [`list-field-groups`](list-field-groups.md), [`list-relationships`](list-relationships.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
