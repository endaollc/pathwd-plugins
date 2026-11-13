# cancel-task

Runs with: path_cancel_task(command: "cancel-task", args: "<flags>") (remove).

```
Usage: cancel-task [options]

Cancel a task on a record

Options:
  --record-id <id>  Record ID
  --task-id <id>    Task ID
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --task-id 777`

## Notes

Cancels (skips) an open task on a record, as the task card's cancel does; PATH records who cancelled it. A cancelled task can't be reopened with these commands: `create-task` makes a new one.

**Examples**

```
cancel-task --record-id 6ab15867998ea2c27b374a34 --task-id 47690
```

- Who may cancel: anyone who can open the record when the task is set to "Can Be Skipped" (`skippable` in `list-record-tasks`) and not to "Only Assignee Can Finish"; the task's creator or an Admin always; the assignee of an "Only Assignee Can Finish" task when it can also be skipped.
- With "Edit Permission Required To Finish" on, the user also needs Edit on the record's business object.
- A task linked to a pathway is cancelled without running its pathway.

**Output:** one line, `Cancelled task 47690 "Call the customer" on record 6ab15867998ea2c27b374a34`. `--quiet`: the task id. It no longer appears in `list-record-tasks` or `list-user-tasks`.

**Common errors**

- `<user> can't cancel task "X" — this task can't be cancelled by this user, as in the web app.`: not skippable for them; its creator or an Admin can cancel it.
- `<user> can't cancel task "X" — you need edit permission on this record to finish this task, as in the web app.`
- `Task … not found on record … (list-record-tasks --record-id … lists its pending tasks).`: already completed or cancelled, the id is wrong, or the task belongs to another record; `Record … not found.` for a wrong record id.

**Related:** `complete-task`, `edit-task`, `list-record-tasks`, `create-task`.

Related commands: [`create-task`](create-task.md), [`edit-task`](edit-task.md), [`assign-task`](assign-task.md), [`complete-task`](complete-task.md).
