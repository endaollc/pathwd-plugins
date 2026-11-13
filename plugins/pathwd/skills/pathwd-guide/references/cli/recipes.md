# Recipes: common requests as commands

Tasks users ask for that take several commands: the commands in order, what the user confirms, and the pitfalls. Every command's own syntax and output: its command note. Values in examples (ids, names, dates) are illustrations: each account has its own.

A rule that runs through all of them: **a pathway that does the job is the business's own process** (it fills the right fields, links the right records, moves milestones, sends what should be sent), so when `pathway-run available` offers one that fits, it is usually better than the same change made with `create-record` / `update-record`. And every change, email or run is something the user agreed to: reads need no confirmation, writes do.

On PATHwd each command runs through the tool for its area; each recipe lists them. Read tools change nothing; add, change and remove tools do. A command's note: each command's page in [the command index](../commands.md).

## 1. "What's on my plate today?"

```
list-user-tasks
list-calendar-events --start-date today --end-date today
list-feed --unread --per-page 25
```

- `list-user-tasks` lists the user's pending tasks (every one, as the dashboard's task list) as a short table: id, task, record (`Job 1347`), record_id and due, already on the user's clock. Group by overdue / due today / later.
- `list-calendar-events` with a bare day covers that whole day in the user's time zone; it lists every origin (calendar events, record appointments, Google / Office 365).
- `list-feed --unread` is one page (25); a full page means there are more.
- Each task's row already names its record; open one (`get-record --record-id <record_id>`) only for the few the user asks about: one command per record counts against the rate limit.

Tools: `path_my_work` (list-user-tasks, list-feed), `path_calendars` (list-calendar-events), `path_find_records` (get-record).

## 2. "Catch me up on Job 26-0074"

```
search --query "26-0074" --bo-name Job
get-record --record-id 6a9056455ff33756afba5084
list-related-records --record-id 6a9056455ff33756afba5084
list-record-messages --record-id 6a9056455ff33756afba5084
list-record-notes --record-id 6a9056455ff33756afba5084
list-record-emails --record-id 6a9056455ff33756afba5084
get-history-by-record --record-id 6a9056455ff33756afba5084 --from 2026-10-01
```

- `get-record` gives the fields, milestones, linked records, open tasks (`Open tasks on this record`) and the pathways its Pathways tab offers (`Pathways you can start on this record`).
- `list-related-records` without a relationship lists each related list with a count; add `--relationship-id <id>` for one list's rows (the Quotes on a Job).
- `get-history-by-record --from` limits the history to recent changes: who changed what, which pathways ran, which milestones were reached.
- Summarize as the user would: stage (milestones), what's open (tasks), what happened lately (history, messages, notes), what's next (pathways offered).

Tools: `path_find_records` (search, get-record, list-related-records), `path_record_activity` (messages, notes, emails, history).

## 3. Find records by their values ("retail jobs won last month with no deposit")

```
find-records --bo-name Job --catalog
find-records --bo-name Job --logic 'INTHELAST(STEPFIRSTOCCURRED("Job Won"), DAYS(30)) AND [Department] = "Retail"' --count
find-records --bo-name Job --logic '…' --columns "Job Name, Department, Job Main Customer" --all
```

- `--catalog` lists the fields the user can search with their kinds and options; the formula language is in [find-records-syntax.md](../../../pathwd-find-records/references/find-records-syntax.md).
- `--count` first: a large count says to narrow the question before listing everything.
- `search` is the quick search box (by name or number, 15 a page): it doesn't evaluate conditions.

Tools: `path_find_records`.

## 4. Change one field on many records (bulk update)

```
find-records --bo-name Job --logic '[Department] = "Trade" AND INMILESTONE("Lead")' --columns "Job Name, Department" --all
list-fields --bo-name Job
get-record --record-id 6a9056455ff33756afba5084 --raw
update-record --record-id 6a9056455ff33756afba5084 --data '{"fields":[{"field_id":191620,"value":"Retail"}]}'
get-record --record-id 6a9056455ff33756afba5084
```

1. Find the records and show the user the list (identifier and current value). They confirm the list and the new value.
2. `pathway-run available --record-id <one of them>`: a pathway that makes this change (and the related ones) is the better route, run once per record.
3. Otherwise get the field's id, type and options from `list-fields` (`get-field --field-id <id>` when its options are cut short), and the current stored form from `get-record --raw` on one record. The value goes in its stored form ([field-values.md](field-values.md)): a Selection's exact option text, Percent as a fraction, dates as UTC ISO with date-only fields at noon on the user's clock.
4. One `update-record` per record. There is no bulk command. Where commands are rate-limited (60 a minute on PATHwd), a "Too many commands" refusal changed nothing and is repeated after a pause.
5. Read one record back to check the value and what the save recalculated, then report record by record (which changed, which were refused and why).

- A refusal on one record (`… can't edit record …`) doesn't stop the others; report it.
- Calculated fields, milestones, Notes, appointment and file fields aren't changed this way (`update-record` note).

Tools: `path_find_records` (find-records, get-record), `path_data_model` (list-fields), `path_pathways` (pathway-run available), `path_update_record` (update-record).

## 5. Find duplicates and archive them

```
search --query "Smith Plumbing" --bo-name Customer
find-records --bo-name Customer --logic 'CONTAINS([Customer Name], "Smith")' --columns "Customer Name, Phone, Email" --all
get-record --record-id 6a9056455ff33756afba5084
list-related-records --record-id 6a9056455ff33756afba5084
archive-record --record-ids 6a9056455ff33756afba5084,6a9056455ff33756afba5085
```

- Search with variants (with and without LLC/Inc, part of the name, the phone number) to find all the candidates.
- Compare each candidate's fields and linked records (`list-related-records` counts): the record with the Jobs, Quotes and history is the one to keep.
- The user picks which to archive. Archiving keeps the record (`unarchive-record` brings it back) but takes it out of lists and searches. There is no merge command: moving links from a duplicate to the kept record is `unlink-record` / `link-record` per link, confirmed with the user.

Tools: `path_find_records`, `path_archive_record` (archive-record), `path_create_record` (link-record).

## 6. Run a saved report and summarize it

```
list-reports
get-report 2496
run-report --report-id 2496 --all
run-report --report-id 2496 --user-filter '[{"label":"Salesperson","values":["Todd Demo"]}]' --all
```

- `list-reports` lists the reports the user can open, with their context (what each is for). `get-report` shows the columns, grouping and filter.
- `run-report` returns 50 rows a page; `--all` returns them all (mind the output limit on very large reports).
- A grouped report already carries per-group totals; summarize those rather than re-adding rows.
- No saved report fits: `find-records --group-by Department --sum "Total Sell With Tax"` answers a grouped question without saving anything; `create-report` saves one (the [`cli-reports-skill` workflow](../../../pathwd-find-records/references/workflows/reports.md)).

Tools: `path_folders_reports` (list-reports, get-report, run-report), `path_find_records` (find-records).

## 7. Export a list

There is no CSV or spreadsheet export command. The list comes back as rows to show:

```
find-records --bo-name Job --logic 'INMILESTONE("Ordering")' --columns "Job Name, Job Main Customer, Total Sell With Tax" --all
run-report --report-id 2496 --all
list-records --folder-id 20976
```

- Show it as a table in the chat; the user can copy it.
- For a file, the web app's report page has **Export** in its actions menu: `web-link report <id>` gives the link.
- Very long lists: narrow the columns (`--columns`), or the rows, to stay under the output limit.

Tools: `path_find_records`, `path_folders_reports`, `path_account` (web-link).

## 8. Move a record forward with a pathway

```
pathway-run available --record-id 6a9056455ff33756afba5084
pathway-run map --pathway-name "Record Deposit" --record-id 6a9056455ff33756afba5084
pathway-run start --pathway-name "Record Deposit" --record-id 6a9056455ff33756afba5084
pathway-run submit --set "Deposit Amount=1500" --set "Deposit Method=Check"
get-record --record-id 6a9056455ff33756afba5084
```

- `available` lists what the record's Pathways tab offers, each with its context (`--from dashboard` lists the dashboard's). Why one the user names isn't offered: `pathway-run map --pathway-name "<name>" --record-id <id>` gives the failed launch checks.
- `map` shows every screen the run will ask before anything runs, so the questions can be asked up front.
- Confirm with the user before `start`: a run changes real data. Each screen is answered only with what the user said ([pathway-screens.md](../../../pathwd-pathways/references/pathway-screens.md)); the paused screen's `Next:` lines give the exact commands.
- Afterwards `get-record` shows the new milestones. A record that didn't move: the [`path-milestone-explain` guide](../../../pathwd-history/references/milestone-explain.md) (recipe 12).

Tools: `path_pathways` (available, map, show), `path_run_pathway` (start, submit, act, cancel), `path_find_records` (get-record).

## 9. Complete a task that has a pathway

```
get-record --record-id 6a9056455ff33756afba5084
pathway-run map --pathway-id 41674 --record-id 6a9056455ff33756afba5084
pathway-run start --record-id 6a9056455ff33756afba5084 --task-id 9071
```

- `get-record`'s `Open tasks on this record` (or `list-record-tasks`) gives each task's pathway (`completing it runs pathway <id> "<name>"`). A task with a pathway is completed by running it, as clicking it in the web app does; `complete-task` refuses it and names the pathway.
- The user confirms the run first; a Complete Task step in the pathway closes the task.
- A task without a pathway: `complete-task --record-id <id> --task-id <id>` (it can't be reopened). Someone else's task: `assign-task`, or `edit-task --assign-to`, when the task allows it.

Tools: `path_find_records` (get-record), `path_pathways` (map), `path_run_pathway` (start, submit), `path_manage_tasks` (complete-task, assign-task, edit-task).

## 10. Create a record and link it ("new Job for Smith Plumbing")

```
pathway-run available --from dashboard
search --query "Smith Plumbing" --bo-name Customer
list-fields --bo-name Job
create-record --bo-name Job --data '{"fields":[{"field_id":191620,"value":"Retail"}]}'
list-relationships --bo-name Job
link-record --record-id <new job id> --related-record-id <customer id> --relationship-id <relationship id>
```

- A dashboard pathway that creates Jobs ("New Job") is the usual route: it asks for the customer and address and links them itself.
- Otherwise: find the customer first, create the record with stored values, then link. A relationship belongs to one business object (the side that holds the link, e.g. a Job's "Job Main Customer"): `link-record --record-id` is that side's record. The [`path-concepts` guide](../concepts.md)'s [relationships.md](../concepts/relationships.md) explains direction.
- An address is its own record linked by a relationship, not a field.
- Confirm the values with the user before creating; `get-record` on the new record shows it as the web app does.

Tools: `path_pathways`, `path_run_pathway`, `path_find_records`, `path_data_model` (list-fields, list-relationships), `path_create_record` (create-record, link-record).

## 11. Send an email with an attachment

```
list-record-files --record-id 6a9056455ff33756afba5084
send-record-email --record-id 6a9056455ff33756afba5084 --to pat@example.com --subject "Revised quote" --body "Hi Pat, the revised quote is attached." --record-file "Revised Quote.pdf" --preview
send-record-email --record-id 6a9056455ff33756afba5084 --to pat@example.com --subject "Revised quote" --body "Hi Pat, the revised quote is attached." --record-file "Revised Quote.pdf"
```

- `send-record-email` sends a real email from the user's name and PATH address, CCs the record's own address (and the user, unless `--no-cc-me`), and saves it to the record's Emails list. It can't be recalled.
- `--preview` shows exactly what would go out. The user confirms the recipients, subject, body and attachments before the real send; addresses come from the user or the record (a Contact's email), never guessed.
- `--record-file` attaches files already on the record (by name or id); `--file` uploads new ones first.
- A pathway with a Send Email step (a quote or invoice pathway) may be the business's usual way to send that email.

Tools: `path_record_activity` (list-record-files), `path_send_email` (send-record-email).

## 12. "Why is this record in (or not in) this milestone?"

```
get-record --record-id 6a9056455ff33756afba5084
list-workflow-milestones --bo-name Service
get-filter milestone 17180 --bo 5051
get-record --record-id 6a9056455ff33756afba5084 --raw
```

- `get-filter milestone` prints the milestone's criteria as a formula (`[Need to Schedule Toggle] = TRUE OR HASSTEP("Need to Schedule")`). Check each rule against the record: field values from `get-record`, workflow steps from `--raw` (`steps`).
- The [`path-milestone-explain` guide](../../../pathwd-history/references/milestone-explain.md) walks this clause by clause.

Tools: `path_find_records` (get-record), `path_data_model` (list-workflow-milestones, get-filter).

## 13. Schedule an appointment

```
list-appointment-fields --bo-name Job
list-calendars
list-calendar-events --start-date 2026-11-20 --end-date 2026-11-20 --calendar-ids 1626
set-appointment 6a9056455ff33756afba5084 --field "Soft Schedule Appointment" --start "2026-11-20 09:00" --end "2026-11-20 11:00" --calendar "Outside Sales"
```

- A record's appointment field is scheduled with `set-appointment` (never `update-record`); it creates or moves the calendar event, titled from the field's Event Description template.
- Times are on the user's clock. Check the day's events for conflicts and confirm the time and calendar with the user.
- A pathway that schedules (on a Service, "Schedule Service Appt") may also notify the customer and move the record on.
- Rescheduling is the same command with the new times; `clear-appointment` removes it (it asks for confirmation).

Tools: `path_data_model` (list-appointment-fields), `path_calendars` (list-calendars, list-calendar-events), `path_update_record` (set-appointment), `path_delete_calendar_items` (clear-appointment).

## 14. Attach a file to a record

1. Bring the file into the workspace: a file attached in the chat with `attach_chat_file` when the client passes it, otherwise `request_file_upload` (the user can drop the file on its upload page). Either gives an `uploads/<id>/<name>` path.

```
list-file-types --bo-name Job
upload-record-file --record-id 6a9056455ff33756afba5084 --file uploads/<id>/final-measurement.pdf --file-type "Final Measurement" --comment "Measured 11/10"
list-record-files --record-id 6a9056455ff33756afba5084
```

- The file type comes from the business object's file types; the user confirms which when it isn't obvious.
- A pathway with an Upload File screen (a signed contract or measurement step) may be where the business expects it.

Tools: `attach_chat_file` / `request_file_upload`, `path_data_model` (list-file-types), `path_add_to_record` (upload-record-file), `path_record_activity` (list-record-files).

## 15. Reassign or follow up on tasks

```
list-record-tasks --record-id 6a9056455ff33756afba5084
list-users
assign-task --record-id 6a9056455ff33756afba5084 --task-id 9071 --user "Pat Lee"
create-task --record-id 6a9056455ff33756afba5084 --title "Call the customer" --assign-to "Pat Lee" --due 2026-11-14
```

- `list-users` (Admins and User Admins) is a table of names and roles; for anyone else the session context names the users. `assign-task --user`, `create-task --assign-to` and `edit-task --assign-to` take the name as shown there, or an email. A name two users share is refused: ask which person and use their email.
- Whether a task can be reassigned or edited depends on its own settings and who created it ([access-and-visibility.md](access-and-visibility.md)).

Tools: `path_record_activity` (list-record-tasks), `path_account` (list-users), `path_manage_tasks` (assign-task, create-task, edit-task).

## 16. Work in two accounts

```
list-accounts
find-records --account 1234 --bo-name Job --logic 'INMILESTONE("Lead")' --count
find-records --account 5678 --bo-name Job --logic 'INMILESTONE("Lead")' --count
```

- Pass `--account` on every command when comparing: the saved account is only the default, and ids from one account mean nothing in the other ([ids-names-paging-limits.md](ids-names-paging-limits.md)).
- On PATHwd the saved account is shared by every chat on the connection, so another chat's `use-account` changes it.

Tools: `path_account`, `path_find_records`.
