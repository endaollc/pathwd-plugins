# create-task

Runs with: path_manage_tasks(command: "create-task", args: "<flags>") (change).

```
Usage: create-task [options]

Create a task on a record, like the record viewer's New Task: from a task
template that the record's current milestones offer (--template; list them with
--list-templates), or a custom task (--title). Anyone who can open the record
can create one. A template fills the title, assignee and settings; the other
options change them

Options:
  --record-id <id>                               Record ID
  --template <id|title>                          A task template offered on this record (see --list-templates)
  --list-templates                               List the task templates offered on this record and stop
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

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --title "Call the customer" --due 2026-11-14`

## Notes

Adds a task to a record, as the record viewer's New Task does: from a task template the record's current milestones offer, or a custom task with its own title. Anyone who can open the record can create one.

**Examples**

```
create-task --record-id 6a974f2da374375d4c083653 --list-templates
create-task --record-id 6a974f2da374375d4c083653 --template "Complete Final Measurement" --due 2026-11-20
create-task --record-id 6a974f2da374375d4c083653 --title "Call the customer about the delivery window" --assign-to jane@example.com --due "2026-11-18 14:00"
create-task --record-id 6a974f2da374375d4c083653 --title "Order replacement screen" --assign-to "Jane Smith" --only-assignee-can-finish yes --can-be-skipped no
```

- `--list-templates` lists the templates offered right now and creates nothing: enabled, available in a milestone the record is in, and whose complete / cancel rules don't already hold. A template already open on the record shows its task in `open`; a record holds one open task per template.
- `--template` (id or title) fills the title, assignee and settings from the template; any other option given changes them. A template linked to a pathway gives the task that pathway: completing it runs the pathway.
- `--title` alone makes a custom task (no pathway, unassigned unless `--assign-to`).
- `--assign-to` takes an active user's name as `list-users` shows it, their email or id (a name two users share is refused, asking for the email); `none` leaves it unassigned.
- `--due` is read on the signed-in user's clock: `2026-11-20` is noon that day, `"2026-11-20 14:00"` that time, `tomorrow` noon tomorrow, `+3d` 72 hours from now.
- The yes/no options take `yes`/`no` (or `true`/`false`, `on`/`off`). Unset ones are off for a custom task.

**Output:** one line with the new task id, then (when any) the settings that are on and its pathway:

```
Created task 72702 "Call the customer about the delivery window" on record 6a974f2da374375d4c083653, assigned to Jane Smith, due 2026-11-18 14:00
  settings: can be skipped, notify me when done; completing it runs pathway 25886
```

`--quiet`: the task id. `--list-templates`:

```
id   | title                      | open
-----|----------------------------|-------------------------
5845 | Create A Quote
5851 | Complete Final Measurement | task 72690 (can't add again)
[2 templates]

Template context:
  Complete Final Measurement (5851): Measure every opening before ordering.
```

`open`: the template's task already open on the record. `Template context` is the designer's note on templates that have one. A `⚠ criteria not decidable for: …` line names templates whose rules couldn't be checked. With none offered: `No task templates offered on record …: a custom task only (create-task --record-id … --title "<title>").`

**Common errors**

- `Pass --title "<title>" for a custom task, or --template <id|title> (see --list-templates)`.
- `Task template "X" isn't offered on record … (templates come from the record's current milestones). Offered: …`: pick one listed, or make a custom task.
- `Record … already has an open "X" task (task 72702); a record holds one open task per template…`: change that one with `edit-task --task-id`.
- `No active user "X" (use an id, email or full name)`; `Can't read "next Friday" as a date…`: use an explicit date.
- `<user> can't create a task on record … — the record is archived (its tasks are hidden), as in the web app.`
- `No task was created on record …: the server accepted the request but no new open task appeared…`: a disabled template, or one already open.

**Related:** `list-record-tasks`, `edit-task`, `assign-task`, `complete-task`, `list-workflow-tasks --bo-name` (every template of a business object).

Related commands: [`edit-task`](edit-task.md), [`assign-task`](assign-task.md), [`complete-task`](complete-task.md), [`cancel-task`](cancel-task.md).
