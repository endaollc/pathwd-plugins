---
name: pathwd-guide
description: "PATHwd reference for the PATHwd connector: what PATH terms mean (business objects, records, fields, milestones, workflow steps, tasks, folders, tags), the account and its session context, which connector tool runs each PATH command and its exact flags, and how records, messages, notes, files, emails, tasks, notifications and calendars are read, created and changed. Covers questions about a PATHwd account's data and how to work with it."
---

# PATHwd

PATHwd is a business system that runs a company's jobs, quotes, services, purchase orders and other records through their workflow. The PATHwd connector (`https://mcp.pathwd.app/mcp`) is signed in as the user, so every read and change runs with that user's own PATH access, exactly as in the PATH web app.

This skill is the index of PATH's documentation bundled with the plugin: PATH's concepts, the account and session, the connector's tools and every command they run, and the guides for records and their activity. Three more skills cover their own areas:

- **pathwd-find-records**: Logic Builder formulas, advanced searches, folders and reports.
- **pathwd-history**: a record's history, and why a record is or isn't in a milestone.
- **pathwd-pathways**: pathways, pathway runs and their screens.

## How the connector runs PATH commands

Every PATH operation is a PATH CLI command line: a command name and its flags. Each connector tool runs the commands of one area and kind. A command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`. Read tools change nothing; add, change and remove tools change PATH data. Two more tools handle files (`request_file_upload` and `write_workspace_file`; in ChatGPT also `attach_chat_file`), and `path_docs` serves the same documentation this plugin carries, from the server.

| Tool | Kind | Commands |
|------|------|----------|
| `path_account` | read | [`list-accounts`](references/commands/list-accounts.md), [`use-account`](references/commands/use-account.md), [`get-account`](references/commands/get-account.md), [`session-context`](references/commands/session-context.md), [`get-profile`](references/commands/get-profile.md), [`get-notification-settings`](references/commands/get-notification-settings.md), [`list-users`](references/commands/list-users.md), [`list-roles`](references/commands/list-roles.md), [`web-link`](references/commands/web-link.md), [`clear-cache`](references/commands/clear-cache.md) |
| `path_data_model` | read | [`list-business-objects`](references/commands/list-business-objects.md), [`get-business-object`](references/commands/get-business-object.md), [`list-fields`](references/commands/list-fields.md), [`get-field`](references/commands/get-field.md), [`list-field-groups`](references/commands/list-field-groups.md), [`list-relationships`](references/commands/list-relationships.md), [`list-workflow-milestones`](references/commands/list-workflow-milestones.md), [`list-workflow-steps`](references/commands/list-workflow-steps.md), [`list-workflow-tasks`](references/commands/list-workflow-tasks.md), [`list-tag-groups`](references/commands/list-tag-groups.md), [`list-file-types`](references/commands/list-file-types.md), [`list-appointment-fields`](references/commands/list-appointment-fields.md), [`parse-filter`](../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../pathwd-find-records/references/commands/get-filter.md) |
| `path_find_records` | read | [`search`](references/commands/search.md), [`find-records`](../pathwd-find-records/references/commands/find-records.md), [`list-records`](references/commands/list-records.md), [`get-record`](references/commands/get-record.md), [`list-related-records`](references/commands/list-related-records.md) |
| `path_record_activity` | read | [`list-record-files`](references/commands/list-record-files.md), [`download-record-file`](references/commands/download-record-file.md), [`list-record-notes`](references/commands/list-record-notes.md), [`list-record-messages`](references/commands/list-record-messages.md), [`list-record-emails`](references/commands/list-record-emails.md), [`list-record-tasks`](references/commands/list-record-tasks.md), [`get-history-by-record`](../pathwd-history/references/commands/get-history-by-record.md), [`get-history-session`](../pathwd-history/references/commands/get-history-session.md), [`get-event-metadata`](../pathwd-history/references/commands/get-event-metadata.md) |
| `path_my_work` | read | [`list-user-tasks`](references/commands/list-user-tasks.md), [`list-notifications`](references/commands/list-notifications.md), [`list-feed`](references/commands/list-feed.md), [`feed-unread-counts`](references/commands/feed-unread-counts.md), [`feed-records`](references/commands/feed-records.md), [`list-popups`](references/commands/list-popups.md) |
| `path_folders_reports` | read | [`list-folders`](../pathwd-find-records/references/commands/list-folders.md), [`list-reports`](../pathwd-find-records/references/commands/list-reports.md), [`get-report`](../pathwd-find-records/references/commands/get-report.md), [`run-report`](../pathwd-find-records/references/commands/run-report.md) |
| `path_calendars` | read | [`list-calendars`](references/commands/list-calendars.md), [`get-calendar`](references/commands/get-calendar.md), [`list-calendar-groups`](references/commands/list-calendar-groups.md), [`get-calendar-group`](references/commands/get-calendar-group.md), [`list-calendar-events`](references/commands/list-calendar-events.md), [`get-calendar-event`](references/commands/get-calendar-event.md) |
| `path_pathways` | read | [`pathway-run available`](../pathwd-pathways/references/commands/pathway-run-available.md), [`pathway-run map`](../pathwd-pathways/references/commands/pathway-run-map.md), [`pathway-run show`](../pathwd-pathways/references/commands/pathway-run-show.md), [`pathway-run list`](../pathwd-pathways/references/commands/pathway-run-list.md), [`pathway-run log`](../pathwd-pathways/references/commands/pathway-run-log.md) |
| `path_create_record` | add | [`create-record`](references/commands/create-record.md), [`link-record`](references/commands/link-record.md) |
| `path_add_to_record` | add | [`add-record-note`](references/commands/add-record-note.md), [`upload-record-file`](references/commands/upload-record-file.md), [`send-message`](references/commands/send-message.md) |
| `path_send_email` | add | [`send-record-email`](references/commands/send-record-email.md) |
| `path_update_record` | change | [`update-record`](references/commands/update-record.md), [`unarchive-record`](references/commands/unarchive-record.md), [`unarchive-record-file`](references/commands/unarchive-record-file.md), [`set-appointment`](references/commands/set-appointment.md), [`update-record-note`](references/commands/update-record-note.md), [`update-record-file`](references/commands/update-record-file.md), [`update-message`](references/commands/update-message.md) |
| `path_run_pathway` | change | [`pathway-run start`](../pathwd-pathways/references/commands/pathway-run-start.md), [`pathway-run submit`](../pathwd-pathways/references/commands/pathway-run-submit.md), [`pathway-run act`](../pathwd-pathways/references/commands/pathway-run-act.md), [`pathway-run cancel`](../pathwd-pathways/references/commands/pathway-run-cancel.md), [`execute-pathway`](../pathwd-pathways/references/commands/execute-pathway.md), [`run-step`](../pathwd-pathways/references/commands/run-step.md) |
| `path_manage_tasks` | change | [`create-task`](references/commands/create-task.md), [`edit-task`](references/commands/edit-task.md), [`assign-task`](references/commands/assign-task.md), [`complete-task`](references/commands/complete-task.md) |
| `path_my_settings` | change | [`mark-notification-read`](references/commands/mark-notification-read.md), [`mark-all-notifications-read`](references/commands/mark-all-notifications-read.md), [`pin-notification`](references/commands/pin-notification.md), [`update-notification-settings`](references/commands/update-notification-settings.md), [`update-profile`](references/commands/update-profile.md) |
| `path_manage_calendars` | change | [`create-calendar`](references/commands/create-calendar.md), [`update-calendar`](references/commands/update-calendar.md), [`share-calendar`](references/commands/share-calendar.md), [`create-calendar-group`](references/commands/create-calendar-group.md), [`update-calendar-group`](references/commands/update-calendar-group.md), [`share-calendar-group`](references/commands/share-calendar-group.md), [`create-calendar-event`](references/commands/create-calendar-event.md), [`update-calendar-event`](references/commands/update-calendar-event.md) |
| `path_manage_folders_reports` | change | [`create-folder`](../pathwd-find-records/references/commands/create-folder.md), [`update-folder`](../pathwd-find-records/references/commands/update-folder.md), [`create-report`](../pathwd-find-records/references/commands/create-report.md), [`update-report`](../pathwd-find-records/references/commands/update-report.md), [`set-filter`](../pathwd-find-records/references/commands/set-filter.md), [`create-folder-sticker`](../pathwd-find-records/references/commands/create-folder-sticker.md), [`update-folder-sticker`](../pathwd-find-records/references/commands/update-folder-sticker.md), [`hide-folder-sticker`](../pathwd-find-records/references/commands/hide-folder-sticker.md), [`show-folder-sticker`](../pathwd-find-records/references/commands/show-folder-sticker.md), [`create-custom-field`](../pathwd-find-records/references/commands/create-custom-field.md), [`update-custom-field`](../pathwd-find-records/references/commands/update-custom-field.md) |
| `path_archive_record` | remove | [`archive-record`](references/commands/archive-record.md), [`unlink-record`](references/commands/unlink-record.md) |
| `path_remove_from_record` | remove | [`archive-record-file`](references/commands/archive-record-file.md), [`delete-record-file`](references/commands/delete-record-file.md), [`delete-record-note`](references/commands/delete-record-note.md), [`delete-message`](references/commands/delete-message.md) |
| `path_cancel_task` | remove | [`cancel-task`](references/commands/cancel-task.md) |
| `path_delete_calendar_items` | remove | [`delete-calendar`](references/commands/delete-calendar.md), [`delete-calendar-group`](references/commands/delete-calendar-group.md), [`delete-calendar-event`](references/commands/delete-calendar-event.md), [`clear-appointment`](references/commands/clear-appointment.md) |
| `path_delete_folder_report_items` | remove | [`delete-folder-sticker`](../pathwd-find-records/references/commands/delete-folder-sticker.md), [`reset-folder-stickers`](../pathwd-find-records/references/commands/reset-folder-stickers.md), [`delete-custom-field`](../pathwd-find-records/references/commands/delete-custom-field.md) |

The [command index](references/commands.md) describes every command by area, with each command's page: its usage and flags, examples, notes on its output and errors, and related commands.

## PATH's core ideas

- Business objects and records. A business object is a kind of thing the dealer tracks: Jobs, Quotes, Services, Customers, Purchase Orders, Appointments, Invoices and so on. A record is one of them: "Job 1234", "the Smith quote".
- Fields. The pieces of information on a record. Some are typed in; others, such as totals and identifiers, are calculated by PATH and can't be typed over.
- Linked records. Records connect to each other: a Quote belongs to a Job, a Job to a Customer. A record can show its linked record's information, and totals can roll up from linked records (a Job's quote total).
- Milestones. The stages a record is in, such as Lead, Full Quote Out, Ordering or Paid In Full. PATH works them out from the record's workflow steps and field values; nobody sets them by hand.
- Workflow steps. Events recorded on a record ("Job Won", "Delivery Scheduled"), by a person at a time. Recording one re-checks the milestones, which can move the record on.
- Tasks. To-dos on a record, assigned to a person with a due date. Most are created and completed automatically as milestones change, and many carry a pathway that does the work.
- Pathways. Guided step-by-step processes started from the Pathways tab of a record's Record Viewer, such as "Select Winning Quote" or "Record PO Receiving". Most steps are automatic.
- Actions. Automations such as notifications, emails, texts, new tasks and scheduled pathways, which fire when a record enters a milestone, from a pathway step, or on a schedule.
- Folders and tags. Folders are saved, filtered lists of one kind of record ("Deposit Needed", "My Jobs In Full Quote Out"). Tags, such as a location, limit which records each user sees anywhere in PATH, so two users can see different records.
- Reports. A report is a table of one kind of record that meet its rules, with the columns its creator picked: fields, fields of linked records, the date a record entered a milestone or had a workflow step, its current milestones, a count, and calculated columns such as a margin or the time between two dates.
- Saving. Saving a record recalculates its calculated fields, re-checks its milestones (tasks and actions may follow), and updates totals on linked records, sometimes a moment later.
- Context. Designers can write *context* on almost anything they set up: the account, business objects, fields, relationships, milestones, workflow steps, tasks, stickers, pathways and pathway steps. Folders and reports have context too, written by whoever can edit them.
- Process context. Designer-written guidance on a business object for one role: how the business works with that kind of record, for the people in that role.
- More terms. [glossary.md](references/concepts/glossary.md) defines, A to Z, the other words commands and their output use; [permissions.md](references/concepts/permissions.md) covers roles and permissions.

Pathway screens save real data when they are submitted.

More in [path-concepts](references/concepts.md).

## Guides

- [path-cli](references/commands.md): PATH CLI command reference (the PATH user app): the exact syntax, flags and subcommands of PATH CLI commands, which PATHwd tool runs each, and an overview of what they cover: records, folders, reports…
- [path-concepts](references/concepts.md): What PATH and PATHwd are and how they work, in the words of the web app.
- [path-session](references/session.md): The PATH account and session on PATHwd — which accounts the user can open, picking one (saved with the connection, so later commands don't need `--account`), its session context, switching accounts…

Step-by-step workflows:

- [record-activity (workflow)](references/workflows/record-activity.md): Use when the user wants to interact with a specific record's activity — messages, notes (Notes fields), emails, files, or tasks, including editing or deleting messages and files and sending emails.
- [records (workflow)](references/workflows/records.md): Use when the user wants to create, update, find, inspect, or archive records in PATH.
- [scheduling (workflow)](references/workflows/scheduling.md): Use when the user wants to work with calendars and appointments in PATH.
- [tasks-notifications (workflow)](references/workflows/tasks-notifications.md): Use when the user wants to manage their personal task queue or notifications.
- [workflow-inspect (workflow)](references/workflows/workflow-inspect.md): Use when the user wants to explore the data model in PATH — business objects, fields, field groups, milestones, steps, tasks, relationships, or folders.

Reference files:

- [Access and visibility](references/cli/access-and-visibility.md): Every PATH CLI command runs as the **acting user** (the signed-in user, or the member named by `--as-user`) and shows and does only what that user could in the web app.
- [Errors and what to do about them](references/cli/errors.md): What a failed PATH CLI command prints, what each kind of message means, and the usual way forward.
- [Field values: what each field type takes](references/cli/field-values.md): How to write a value for every PATH field type, in the two places a value is written:
- [Ids, names, accounts, paging and limits](references/cli/ids-names-paging-limits.md): How PATH CLI commands name things (an id or a name), which account a command runs in, how much a list command returns by default, the output formats, and the limits commands run under on PATHwd.
- [Recipes: common requests as commands](references/cli/recipes.md): Tasks users ask for that take several commands: the commands in order, what the user confirms, and the pitfalls.
- [Actions](references/concepts/actions.md): An action is a single automated job that PATH carries out for a record without anyone filling in a screen, such as sending a notification, emailing a customer or creating a task.
- [Fields](references/concepts/fields.md): A field is one piece of information on a record: a customer's name, a quote total, a job's install date.
- [Folders and Tags](references/concepts/folders-and-tags.md): Folders are saved, filtered lists of records for one business object.
- [Glossary](references/concepts/glossary.md): Terms that PATH commands, their output and the other guides use, A to Z, each with where to read more.
- [PATHwd](references/concepts/path-wd.md): This file explains PATHwd, the standard setup of PATH for window and door dealers, in the business terms a dealer's staff use.
- [Roles and permissions](references/concepts/permissions.md): What a PATH user may see and do comes from the **roles** they hold in the account, their **tags**, and what each folder, report and calendar is shared with.
- [Records and Business Objects](references/concepts/records-and-business-objects.md): Everything in PATH is stored as a record, and every record belongs to a type called a business object.
- [Relationships and Linked Records](references/concepts/relationships.md): Records in PATH are connected to each other: a Quote belongs to a Job, a Job belongs to a Customer.
- [What Happens When a Record Is Saved](references/concepts/saving-records.md): When someone saves a record, or a pathway does it for them, PATH does more than store the typed values.
- [Workflow: Milestones, Workflow Steps and Tasks](references/concepts/workflow.md): Workflow is how a record such as a Job, Quote or Service moves through its lifecycle, through three parts: milestones (the stages the record is in), workflow steps (events recorded on the record) and…

## Docs version

These skills carry PATH docs version `ee0e4f88da75`. The connector reports the version it serves in its session context; when the two differ, `path_docs` has the newer text ([docs-version.md](references/docs-version.md)).
