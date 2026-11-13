# complete-task

Runs with: path_manage_tasks(command: "complete-task", args: "<flags>") (change).

```
Usage: complete-task [options]

Mark a task as complete on a record. A task linked to a pathway isn't completed
here: completing it means running that pathway (as the web app does), which may
change records, so the command exits with an error naming the pathway and the
pathway-run start --record-id <id> --task-id <id> command that runs it —
confirm with the user before starting it

Options:
  --record-id <id>  Record ID
  --task-id <id>    Task ID
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --task-id 777`

## Notes

Marks a task on a record complete.

```
complete-task --record-id 6a9056455ff33756afba5084 --task-id 9071
```

- **A task linked to a pathway isn't completed here.** In the web app, completing it runs the pathway, so the command exits with an error naming the pathway and the command that runs it: `pathway-run start --record-id <id> --task-id <task id>`. That run changes records; the user confirms before it starts.
- A task without a pathway is marked complete. It can't be reopened with these commands.
- A task set to "only the assignee can finish" is completed by its assignee, its creator or an Admin (`assignee only` in `list-record-tasks`; `get-record` marks the tasks the user can't complete).

**Output:** one line, `Completed task 9071 "Call the customer" on record 6a9056455ff33756afba5084`. `--quiet`: the task id.

**Finding the task:** `list-record-tasks --record-id <id>` (each task's `pathway`), `get-record --record-id <id>` (its open tasks), or the user's own `list-user-tasks`.

**Related:** `cancel-task`, `edit-task`, `assign-task`, `pathway-run start --task-id`.

Related commands: [`create-task`](create-task.md), [`edit-task`](edit-task.md), [`assign-task`](assign-task.md), [`cancel-task`](cancel-task.md), [`pathway-run start`](../../../pathwd-pathways/references/commands/pathway-run-start.md).
