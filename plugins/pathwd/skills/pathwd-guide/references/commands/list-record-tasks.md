# list-record-tasks

Runs with: path_record_activity(command: "list-record-tasks", args: "<flags>") (read-only).

```
Usage: list-record-tasks [options]

List tasks on a record

Options:
  --record-id <id>  Record ID
```

## Notes

Lists the open tasks on one record, whoever they are assigned to: the task cards in the record viewer. Completed and cancelled tasks aren't listed.

**Examples**

```
list-record-tasks --record-id 6ab15867998ea2c27b374a34
list-record-tasks --record-id 6a974f2da374375d4c083653 --fields id,task,due,pathway
```

- `--record-id` is the record's 24-character id, not its number ("26-0074"); find it with `search` or `find-records`.
- Every open task comes back at once (no paging). An archived record shows no open tasks.
- `get-record --record-id <id>` shows the same open tasks in short form, with their pathways and whether the signed-in user can complete each.

**Output**

A table of the record's open tasks:

```
id    | task                     | assigned  | due              | pathway                    | settings                 | notes
------|--------------------------|-----------|------------------|----------------------------|--------------------------|------
58281 | Collect Required Deposit | Todd Demo | 2026-11-14 12:00 | Record Job Payment (25886)
58282 | Call the customer        |           |                  |                            | skippable, reassignable  | prefers mornings
[2 tasks]
```

Columns to read:

- `id`: the task id `complete-task`, `cancel-task`, `assign-task`, `edit-task` and `pathway-run start --task-id` take.
- `task` (title), `assigned` (the assignee's name; blank: unassigned), `due` (the user's clock), `notes`.
- `pathway`: completing the task runs that pathway (`pathway-run start --record-id <id> --task-id <task id>`); blank: `complete-task` marks it complete.
- `settings`, the ones that are on: `skippable` (Can Be Skipped: anyone who can open the record can cancel it), `reassignable` (Can Be Assigned By Other Users), `assignee only` (Only Assignee Can Finish), `needs edit` (Edit Permission Required To Finish).

`--quiet`: the task ids only.

**Common errors**

- `<user> can't list the tasks of record <id> — they don't have View permission on Jobs (business object 5050), as in the web app.`
- `ERROR [404]: Not Found`: not a record id (a record number, a typo, or a deleted record).

**Related:** `list-user-tasks` (the user's own, across records), `create-task`, `edit-task`, `assign-task`, `complete-task`, `cancel-task`, `pathway-run available --record-id <id>`.

Related commands: [`list-record-files`](list-record-files.md), [`download-record-file`](download-record-file.md), [`list-record-notes`](list-record-notes.md), [`list-record-messages`](list-record-messages.md), [`list-record-emails`](list-record-emails.md), [`get-history-by-record`](../../../pathwd-history/references/commands/get-history-by-record.md), [`get-history-session`](../../../pathwd-history/references/commands/get-history-session.md), [`get-event-metadata`](../../../pathwd-history/references/commands/get-event-metadata.md).
