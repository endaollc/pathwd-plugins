# list-workflow-steps

Runs with: path_data_model(command: "list-workflow-steps", args: "<flags>") (read-only).

```
Usage: list-workflow-steps [options]

List a business object's workflow steps (id, name, initial/disabled), then each
one's context

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
```

## Notes

The workflow steps of one business object: the events a record can have recorded on it (Job Created, Full Quote Sent to Customer, Job Won, Deposit Received). Steps are what milestone criteria mostly check; recording a step re-checks the record's milestones.

**Examples**

```
list-workflow-steps --bo-name Job
list-workflow-steps --bo-id 5050
find-records --bo-name Job --logic 'HASSTEP("Job Won") AND INTHELAST(STEPLASTOCCURRED("Job Won"), DAYS(30))'
```

- Step names are what formulas use: `HASSTEP("Job Won")`, `NOTHASSTEP`, `HASALLSTEPS`, and the date `STEPFIRSTOCCURRED("…")` / `STEPLASTOCCURRED("…")` in `find-records`, folders and reports.
- `run-step` records a step on a record; a pathway that records it (with the related changes) is usually the better route (`pathway-run available --record-id <id>`).
- Which milestones depend on a step: `get-filter milestone <id> --bo <business object id>` for each milestone in `list-workflow-milestones`.

**Output**

```
id    | name                         | flags
------|------------------------------|--------
17439 | Job Created                  | initial
17440 | Quick Quote Sent to Customer
17482 | Job Won
[41 steps]
```

- `id`, `name` (what formulas use); `flags`: `initial` (recorded automatically when a record is created), `disabled` (turned off by the designer). Steps don't belong to a milestone; milestones refer to steps.
- `Step context:` under the table lists each step's designer description, when one has it (once per session, like all context).
- A record's own steps: `get-record --record-id <id> --raw` (`steps`: the active step ids; `all_steps`: each `{step_id, active, timestamp, user_id}`, including deactivated ones).

**Common errors**

- `Provide either --bo-id or --bo-name`.
- `Business object "X" not found — did you mean "…"?`: `list-business-objects` lists the names.
- `<user> can't read business object 5050 — they don't have View permission on Jobs (business object 5050), as in the web app.`
- In a formula, `Unknown step "…"`: the name isn't one of this business object's steps; copy it from this list.

**Related:** `list-workflow-milestones`, `list-workflow-tasks`, `get-filter`, `find-records --catalog` (also lists the steps), path-concepts' [workflow.md](../concepts/workflow.md).

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`get-field`](get-field.md), [`list-field-groups`](list-field-groups.md), [`list-relationships`](list-relationships.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
