# Workflow: tasks-notifications

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../commands.md) gives any command's syntax and the tool that runs it.

**Covers:** the user's own work: the tasks assigned to them, completing or cancelling tasks (and running a task's pathway), notifications, the Feed and popups. Commands: `list-user-tasks`, `complete-task`, `cancel-task`, `pathway-run start --task-id`, `list-notifications`, `list-feed`, `list-popups`, `mark-notification-read`, `mark-all-notifications-read`. Each command's syntax and output: its command note.

The account and session: the [`path-session` guide](../session.md). Every command's syntax: the [`path-cli` guide](../commands.md).

## What to ask the user

**For listing tasks or notifications:** Nothing extra — just run the command.

**For completing a task:**
1. **Which record** — a record ID or enough info to find it
2. **Which task** — the task name (you'll look up the task ID)

If the user hasn't specified an account ID, ask which account to use or run the [`path-session` guide](../session.md).

## Steps

### Listing Your Tasks

```
--account <ID> list-user-tasks
```

This lists the open (pending) tasks assigned to the current user across all records; tasks on archived records are left out. It is a short table in every format (JSON too): `id`, `task`, `record` (the record's name, e.g. `Job 1347`), `record_id` and `due` (already on the user's clock), soonest due first. They read best grouped by record, showing:
- Task name
- Record name
- Due date

`id` and `record_id` are what `complete-task`, `cancel-task`, `edit-task` and `assign-task` take. A task's notes, settings and pathway: `get-record --record-id <record_id>` (`open_tasks`).

### Completing a Task

#### Step 1: Find the Task

If the user gave a record ID, list tasks on that record:
```
--account <ID> list-record-tasks --record-id <id>
```

If not, search for the record first:
```
--account <ID> search --query "<text>"
```

#### Step 2: Check for a pathway

A task can be linked to a pathway. In the web app, completing such a task runs its pathway (its screens, then a Complete Task step closes the task). `get-record --record-id <id>` lists the record's open tasks under `Open tasks on this record`, each with `completing it runs pathway <id> "<name>"` (and that pathway's context) when it has one.

- **No pathway:** complete it directly (Step 3).
- **A pathway:** `complete-task` refuses it, naming the pathway and the command that runs it. The run changes records, so the user confirms first; then it runs as any pathway does (the [`cli-pathways` workflow](../../../pathwd-pathways/references/workflows/pathways.md)): `pathway-run start --record-id <id> --task-id <task_id>`.

#### Step 3: Complete the Task

```
--account <ID> complete-task \
  --record-id <id> \
  --task-id <task_id>
```

#### Step 4: Confirm

> "Done! I completed the **Review Quote** task on Job JOB-1234."

### Cancelling a Task

```
--account <ID> cancel-task --record-id <record_id> --task-id <task_id>
```

### Listing Notifications

```
# All notifications
--account <ID> list-notifications

# Unread only
--account <ID> list-notifications --unread
```

### Marking Notifications as Read

```
# Mark a single notification as read (feed medium; one call per notification —
# there is no bulk-by-ids command)
--account <ID> mark-notification-read <notificationId>

# Mark a single POPUP notification as read (popup medium only)
--account <ID> mark-notification-read <notificationId> --popup

# Mark all notifications as read (the feed medium unless --mediums says
# otherwise; --record-id <id> limits it to one record's)
--account <ID> mark-all-notifications-read
```

Read state is tracked per medium (feed vs popup) — marking the feed copy read
does not mark the popup copy, and vice versa.

### Viewing Unread Counts by Business Object

```
--account <ID> feed-unread-counts
```

`--by record` counts per record instead.

## CLI Commands Used

| Command | Purpose |
|---------|---------|
| `list-user-tasks` | The current user's open tasks: id, task, record, record_id, due (tasks on records hidden from them by tag visibility are left out) |
| `list-record-tasks --record-id <id>` | List tasks on a specific record |
| `complete-task --record-id <id> --task-id <id>` | Mark a task as complete |
| `cancel-task --record-id <id> --task-id <id>` | Cancel (skip) a task |
| `assign-task --record-id <id> --task-id <id> --user <name\|email>` | Assign a task to a user (the name as `list-users` or the session context shows it) |
| `create-task --record-id <id> [--template <id\|title> \| --title <t>] [--assign-to …] [--due …] [--notes …]` | Create a task (New Task); `--list-templates` lists the templates offered |
| `edit-task --record-id <id> --task-id <id> [--title …] [--assign-to …] [--due …] [--notes …] …` | Edit an open task (creator/admin: everything; others: the assignee only, when allowed) |
| `list-notifications` | List notifications |
| `list-notifications --unread` | List unread notifications only |
| `get-record --record-id <id>` | Get record context for a task |
| `search --query <text>` | Find a record to act on |
| `mark-notification-read <id>` | Mark one notification as read (feed) |
| `mark-notification-read <id> --popup` | Mark one popup notification as read |
| `mark-all-notifications-read [--mediums <list>] [--record-id <id>]` | Mark all notifications as read (feed by default) |
| `feed-unread-counts [--by bo\|record]` | Unread counts per business object (or per record) |

## Key facts and confirmations

- Every command here runs through the PATH WD Remote MCP tool for its area (each command's page in [the command index](../commands.md) names it).
- Completing or cancelling a task can't be undone with these commands, and completing a task that has a pathway means running that pathway: the user confirms first.
- Users name tasks ("the Review Quote task"); ids come from `list-user-tasks` / `list-record-tasks`. Two tasks with similar names on one record are listed and the user picks.
- Tasks read best with their name, record and status (grouped by record, overdue first); notifications with what happened, on which record and when, unread ones marked.
