# list-workflow-tasks

Runs with: path_data_model(command: "list-workflow-tasks", args: "<flags>") (read-only).

```
Usage: list-workflow-tasks [options]

List a business object's workflow tasks (id, title, pathway, assignment, stored
rules), then each one's context

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
```

## Notes

The task templates of one business object, as set up in the Designer: the tasks PATH creates on records ("Collect Required Deposit", "Follow Up On Quote") with who they go to, when they are due, the pathway they carry and the criteria that complete or cancel them. These are definitions, not the tasks open on a record.

**Examples**

```
list-workflow-tasks --bo-name Job
list-workflow-tasks --bo-id 5050 --fields id,title,pathway
get-filter task-complete 5838 --bo 5050
```

- The last line prints a template's auto-complete criteria as a formula (`[Payments Recorded] > 0`). `task-cancel` and `task-filter` (when it is available) read the other two; each needs the business object: `get-filter task-cancel 5838 --bo 5050`.
- Which milestones create or offer a task: the `creates` / `offers` columns of `list-workflow-milestones` hold its id.

**Output**

```
id   | title                        | pathway                             | assign       | rules                                 | flags
-----|------------------------------|-------------------------------------|--------------|---------------------------------------|----------
5842 | Complete Scheduled Outreach  | Complete Scheduled Outreach (25870) | creator      | task-complete
5839 | Complete A Customer Outreach |                                     | relationship | task-cancel                           | skippable
5847 | Create a Sales Order         | Create Job Sales Order (25920)      | creator      | task-complete task-cancel task-filter | skippable
[22 tasks]
rules: auto-complete (task-complete), auto-cancel (task-cancel), availability (task-filter); print one with get-filter <rule> <id> --bo 5050.
```

Columns to read:

- `id`, `title` (`create-task --template` takes either).
- `pathway`: the pathway that completing the task runs (`pathway-run start --task-id` runs it on a record's task).
- `assign`: `creator` (whoever creates the task), `user` (one named user) or `relationship` (the person the record links to, such as the Job's Outside Salesperson).
- `rules`: the stored criteria, named as `get-filter` targets: `task-complete` (auto-complete), `task-cancel` (auto-cancel), `task-filter` (when the task is available).
- `flags`: `skippable` (can be skipped), `disabled`.
- `Task context:` under the table lists each template's designer description, when one has it (once per session, like all context).
- A record's actual tasks are `get-record` (`Open tasks on this record`) and `list-record-tasks`; the user's own are `list-user-tasks`.

**Common errors**

- `Provide either --bo-id or --bo-name`.
- `Business object "X" not found — did you mean "…"?`: `list-business-objects` lists the names.
- `<user> can't read business object 5050 — they don't have View permission on Jobs (business object 5050), as in the web app.`
- `get-filter task-complete 5838` (no business object): `The task-complete target needs --bo <id>.`

**Related:** `create-task --template` (add a task from a template to a record), `complete-task`, `list-workflow-milestones`, `get-filter`, path-concepts' [workflow.md](../concepts/workflow.md).

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`get-field`](get-field.md), [`list-field-groups`](list-field-groups.md), [`list-relationships`](list-relationships.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
