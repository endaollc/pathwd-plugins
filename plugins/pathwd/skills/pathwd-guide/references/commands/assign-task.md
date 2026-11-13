# assign-task

Runs with: path_manage_tasks(command: "assign-task", args: "<flags>") (change).

```
Usage: assign-task [options]

Assign a task on a record to a user: --user takes the name list-users shows (or
an email or id); a name two users share is refused, asking for the email

Options:
  --record-id <id>        Record ID
  --task-id <id>          Task ID (as listed by list-record-tasks)
  --user <name|email|id>  The user to assign it to: their name as list-users
                          shows it, their email or their id
  --user-id <id>          User ID to assign the task to (or use --user)
  --data <json>           Legacy JSON body {"task_id": .., "user_id": ..};
                          --task-id/--user-id win
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --task-id 777 --user-id 12`

## Notes

Gives an open task on a record to another user.

**Examples**

```
assign-task --record-id 6ab15867998ea2c27b374a34 --task-id 47691 --user "Jane Smith"
assign-task --record-id 6ab15867998ea2c27b374a34 --task-id 47691 --user jane@example.com
assign-task --record-id 6a974f2da374375d4c083653 --task-id 41255 --user-id 8
```

- `--user` takes the user's name as `list-users` shows it (or their email or id); a name two users share is refused, asking for the email. `--user-id` takes an id. To unassign, use `edit-task --assign-to none`.
- `--task-id` is the task's id from `list-record-tasks` or `get-record` (its open tasks), not its template's id.
- Who may reassign: the task's creator or an Admin always; anyone else when the task is set to "Can Be Assigned By Other Users".
- Reassigning doesn't change the task's other settings, due date or pathway.

**Output:** one line, `Assigned task 47691 "Schedule the service visit" on record 6ab15867998ea2c27b374a34 to Jane Smith`. `--quiet`: the task id.

**Common errors**

- `<user> can't assign task "X" — this task's assignee can't be changed by this user, as in the web app.`
- `Record … not found.` / `Task 47691 not found on record … (list-record-tasks --record-id … lists its pending tasks).`: the id is wrong, the task is completed or cancelled, or it belongs to another record. Ids are checked before permissions.
- `User "X" not found: no active user in this account has that name, email or id (list-users lists them).`
- `"X" is the name of 2 users in this account; give that user's email address instead.`: ask the user which person they mean.
- `Missing --user <name|email|id> (or --user-id <id>).`

**Related:** `edit-task`, `list-record-tasks`, `list-users`, `create-task --assign-to`.

Related commands: [`create-task`](create-task.md), [`edit-task`](edit-task.md), [`complete-task`](complete-task.md), [`cancel-task`](cancel-task.md), [`list-users`](list-users.md).
