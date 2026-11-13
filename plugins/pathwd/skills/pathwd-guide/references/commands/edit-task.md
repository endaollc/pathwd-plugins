# edit-task

Runs with: path_manage_tasks(command: "edit-task", args: "<flags>") (change).

```
Usage: edit-task [options]

Edit an open task on a record, like a task's Edit button. Its creator or an
admin can change every option; anyone else only the assignee (--assign-to), and
only when the task is set to "Can Be Assigned By Other Users"

Options:
  --record-id <id>                               Record ID
  --task-id <id>                                 Task ID (as listed by list-record-tasks or get-record's open_tasks)
  --title <text>                                 Task title
  --assign-to <user>                             Assignee: an active user's id, email or full name; "none" to unassign
  --due <date>                                   Due date: YYYY-MM-DD, "YYYY-MM-DD HH:mm", today, tomorrow, +3d (your time zone); "none" to clear
  --notes <text>                                 Notes
  --notify-me-when-done <yes|no>                 Notify the creator when the task is done
  --notify-assignee-when-due <yes|no>            Notify the assignee when the task is due
  --can-be-assigned-by-others <yes|no>           Other users may change the assignee
  --can-be-skipped <yes|no>                      The task can be cancelled (skipped)
  --only-assignee-can-finish <yes|no>            Only the assignee (or creator / an admin) can complete it
  --edit-permission-required-to-finish <yes|no>  Users who can't edit the record can't complete or cancel it
```

## Notes

Changes an open task on a record, as the task's Edit button does: title, assignee, due date, notes and its settings. Only the options given change.

**Examples**

```
edit-task --record-id 6ab15867998ea2c27b374a34 --task-id 47691 --due "2026-11-19 10:00"
edit-task --record-id 6ab15867998ea2c27b374a34 --task-id 47691 --assign-to jane@example.com
edit-task --record-id 6ab15867998ea2c27b374a34 --task-id 47691 --title "Schedule the service visit" --notes "Customer prefers mornings"
edit-task --record-id 6ab15867998ea2c27b374a34 --task-id 47691 --due none --can-be-skipped yes
```

- Who may change what: the task's creator or an Admin changes every option. Anyone else may only change the assignee, and only when the task is set to "Can Be Assigned By Other Users" (`reassignable` in `list-record-tasks`).
- `--assign-to` takes an active user's name as `list-users` shows it, their email or id (a name two users share is refused, asking for the email); `none` unassigns. (`assign-task --user` does the same but can't unassign.)
- `--due` is read on the signed-in user's clock (`2026-11-19` is noon that day); `none` clears it.
- The yes/no options take `yes`/`no`; they are the same settings `create-task` sets.
- A task linked to a pathway keeps its pathway; editing never completes it.

**Output:** the task after the save, one line, then (when any) the settings that are on and its pathway:

```
Updated task 47691 "Schedule the service visit" on record 6ab15867998ea2c27b374a34, assigned to Jane Smith, due 2026-11-19 10:00
  settings: can be skipped
```

`--quiet`: the task id.

**Common errors**

- `Nothing to change: pass at least one of --title, --assign-to, --due, …`.
- `<user> can't edit task "X" — only its creator or an admin can edit this task, and it is not set to "Can Be Assigned By Other Users", as in the web app.`
- `<user> can't change more than the assignee of task "X" — only its creator or an admin can change its other options, as in the web app.`: send `--assign-to` alone.
- `Task … not found on record … (list-record-tasks --record-id … lists its pending tasks).`: already completed or cancelled, the id is wrong, or the task belongs to another record; `Record … not found.` for a wrong record id.
- `No active user "X" (use an id, email or full name)`; `Can't read "…" as a date…`.

**Related:** `list-record-tasks`, `create-task`, `assign-task`, `complete-task`, `cancel-task`.

Related commands: [`create-task`](create-task.md), [`assign-task`](assign-task.md), [`complete-task`](complete-task.md), [`cancel-task`](cancel-task.md).
