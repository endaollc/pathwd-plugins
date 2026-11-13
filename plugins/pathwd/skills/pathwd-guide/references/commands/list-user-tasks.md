# list-user-tasks

Runs with: path_my_work(command: "list-user-tasks", args: "<flags>") (read-only).

```
Usage: list-user-tasks [options]

Your open tasks (the dashboard's task list), as a short table: id, task, record
("Job 1347"), record_id, due (your time zone), soonest due first. The same
columns in every format; JSON is an array of { id, task, record, record_id, due
}. Act on one with complete-task / cancel-task / edit-task --record-id
<record_id> --task-id <id>; get-record --record-id <record_id> shows the record
and its open tasks in full

Options:
```

## Notes

The signed-in user's open tasks across every record, as the web dashboard's task list: a short table with only what is needed to act on one. Completed and cancelled tasks aren't listed.

**Examples**

```
list-user-tasks
list-user-tasks --fields id,task,due
list-user-tasks --as-user jane@example.com
```

- Every open task comes back at once, soonest due first (no due date last); there is no paging. The rows are short (about 100 characters each).
- Left out, as on the dashboard: tasks on archived records (they come back when the record is unarchived), on records the user's tags hide, and on business objects they can't view.
- `--as-user` lists another member's tasks (account Admins only).

**Output**

The same columns in every format:

- `id`: the task id, which `complete-task`, `cancel-task`, `edit-task` and `assign-task` take.
- `task`: the task's name.
- `record`: the record it is on, as the dashboard's task row names it: the business object and the record's identifier (`Job 1347`).
- `record_id`: the record's 24-character id, which every task command also takes.
- `due`: the due date and time on the user's clock (`2026-11-12 14:30`), empty when there is none. A due date typed as a plain date is noon on the creator's clock.

Text is a table of those columns with a `[N open tasks]` footer. JSON is an array of `{"id", "task", "record", "record_id", "due"}`, one per line. `--quiet` prints the task ids; `--fields` picks some of the columns (any other key is refused).

A task's status is always pending here and its assignee is always the user, so neither is a column. Its notes, settings and pathway are in `get-record --record-id <record_id>` (`open_tasks`, with the pathway name and `can_complete`) or `list-record-tasks --record-id <record_id>`. A task that runs a pathway is finished with `pathway-run start --record-id <record_id> --task-id <id>`; `complete-task` says so when given one.

**Common errors**

- An empty list: nothing open is assigned to the user. Unassigned tasks and other people's don't show here; `list-record-tasks --record-id <id>` lists a record's tasks for everyone.

**Related:** `list-record-tasks`, `get-record`, `complete-task`, `cancel-task`, `edit-task`, `list-feed` (task assignment notices), `pathway-run start --task-id`.

Related commands: [`list-notifications`](list-notifications.md), [`list-feed`](list-feed.md), [`feed-unread-counts`](feed-unread-counts.md), [`feed-records`](feed-records.md), [`list-popups`](list-popups.md), [`cancel-task`](cancel-task.md), [`complete-task`](complete-task.md), [`edit-task`](edit-task.md), [`get-record`](get-record.md).
