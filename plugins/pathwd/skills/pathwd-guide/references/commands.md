# path-cli

> **Commands:** every command in this skill is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](commands.md) gives any command's syntax and the tool that runs it.

The PATH CLI is a compact client for the PATH API. Each PATH WD Remote MCP tool runs the commands of its area and returns their output as compact text: a list is an ASCII table (`id | name | …`, id first, long cells cut with `…`, a footer such as `[12 items]` or `[25 of 140 records — --page 2 for more]`), one item is `key: value` lines, and a change prints a line or two naming what changed with its ids. `--fields <columns>` in `args` picks a table's columns; `format: "quiet"` gives ids only, one per line. The "Commands by tool" table at the end lists which tool runs which command.

This page is the index: what each command is for, by area, and the references that cover more than one command. Each command's page in [the command index](commands.md) gives one command's usage and flags with its note: worked examples, its output and what to read in it, its common errors and related commands. **Every** command's exact flags: [the command index](commands.md); `--help` in a command's args prints its help, e.g. `path_update_record(command: "update-record", args: "--help")`.

## Designer work: PATH's Designer

This connection is the PATH **user app**. Everything that builds or changes how an account is set up is Designer work, done by an Admin or Designer in **PATH's Designer**: business objects, fields, expressions, field groups, relationships, stickers, settings, workflow milestones/steps/tasks, notification rules and actions, file types, document templates, imports and exports, pathways and steps, integrations, account settings, users/roles/tags, and Designer history. Users, roles and tags also need Admin or User Admin. A command that is not in the command reference is Designer work.

## Skills for common tasks

Most requests have a guide with the steps and checks that go with them:

| Task | Use |
|------|-----|
| Sign in, switch account, sign out | the [`path-session` guide](session.md) |
| What a PATH or PATH WD term, business object, process or pathway screen means | the [`path-concepts` guide](concepts.md) |
| Find, create, update, link, archive records | the [`cli-records` workflow](workflows/records.md) |
| Messages, emails, files and tasks on a record | the [`cli-record-activity` workflow](workflows/record-activity.md) |
| What changed on a record, or what a user did | the [`path-record-history` guide](../../pathwd-history/references/record-history.md) |
| Read-only tour of how an account is set up | the [`cli-workflow-inspect` workflow](workflows/workflow-inspect.md) |
| Running pathways (find, list, available, run, test) | the [`cli-pathways` workflow](../../pathwd-pathways/references/workflows/pathways.md) |
| Folders | the [`cli-folders-skill` workflow](../../pathwd-find-records/references/workflows/folders.md) |
| Reports (build one from a plain-language ask, run it, fix it) | the [`cli-reports-skill` workflow](../../pathwd-find-records/references/workflows/reports.md) |
| Folder and report filters (Logic Builder formulas) | the [`cli-logic-builder` workflow](../../pathwd-find-records/references/workflows/logic-builder.md) and the [`path-logic-formula-syntax` guide](../../pathwd-find-records/references/logic-formula-syntax.md) |
| Why a record is or isn't in a milestone | the [`path-milestone-explain` guide](../../pathwd-history/references/milestone-explain.md) |
| Calendars, appointments, notes | the [`cli-scheduling` workflow](workflows/scheduling.md) |
| The value form of each field type (`--data` vs pathway `--set`): percent, dates, time spans, phones, blanks | this skill's [field-values.md](cli/field-values.md) |
| Every pathway screen and how to answer it (`submit`, `act`, `--select`, `--skip`, `--answers`) | this skill's [pathway-screens.md](../../pathwd-pathways/references/pathway-screens.md) |
| `find-records` formulas, flags, recipes and common mistakes | this skill's [find-records-syntax.md](../../pathwd-find-records/references/find-records-syntax.md) |
| Requests that take several commands: what's on my plate, catch me up on a record, bulk updates, duplicates, export a list, send an email with an attachment, schedule, complete a task with a pathway | this skill's [recipes.md](cli/recipes.md) |
| Why a record, field, folder, report or pathway isn't there, or an action is refused; `--as-user`; deactivated members | this skill's [access-and-visibility.md](cli/access-and-visibility.md) |
| Ids vs names, record numbers, which account a command uses, paging defaults per command, output formats, limits | this skill's [ids-names-paging-limits.md](cli/ids-names-paging-limits.md) |
| An error message: what it means and what to do (command line, quotes, accounts, names, refusals, server errors, values, deletes, remote limits) | this skill's [errors.md](cli/errors.md) |
| A term in a command's output (acting user, Custom Field, user filter, calendar group, …); roles and permissions | the [`path-concepts` guide](concepts.md)'s [glossary.md](concepts/glossary.md) and [permissions.md](concepts/permissions.md) |
| A user's own tasks and notifications | the [`cli-tasks-notifications` workflow](workflows/tasks-notifications.md) |
| Building or changing business objects, fields, workflow, pathways, actions, templates, settings, users | PATH's Designer (Admin or Designer) |

## Session Workflow

Use the [`path-session` guide](session.md) to start a session. In short:

1. **Signed in already** — the user signed in when they connected PATH WD; `list-accounts` lists the accounts they can open
2. **Select default account (optional)** — `use-account <id>` so `--account` becomes optional on later commands
3. **Operate** — pass `--account <id>` per command, or rely on the saved default

## Usage

```
# Every command string follows this pattern:
[global-opts] <command> [command-opts]

# Global options (before the command):
#   --account <id>     Account ID (overrides the account saved with use-account)
#   --fields <list>    Comma-separated columns for table output (by header)
#   --quiet            IDs only (one per line)
#   --as-user <user>   Act as another member of the account (id, email or name; account Admins only)
#   --confirm <code>   Confirm a delete with the code it printed, only after the user explicitly said yes (see Deletes are confirmed)
#   --with-context     Repeat designer context (and the session context) already shown this session
```

### Files on this connection

Commands run on PATH WD's server, in a private workspace for this connection, so every file option (`--file`, `--out`, `--answers <file>`, `--context-file`, and `files` / `body_file` in pathway answers) takes a path **relative to that workspace**. A path outside it (starting with `/`, `~` or a drive letter, or using `..`) is refused.

- **A file the user dropped or attached in the chat, or one on their computer** (a photo for an Upload File screen, a contract to add to a record): users expect to just drop it in the chat. Get it into the workspace in this order:
  1. **The client passes it to tools as a file input** (ChatGPT does): call `attach_chat_file` with it. The server fetches it and returns the workspace path.
  2. **Otherwise, if you can run code where the file is** (Claude's code sandbox: an attached file is usually in its uploads folder): call `request_file_upload` with the file's name and run its curl command there yourself, replacing `<local file>`.
  3. **If that fails** (blocked network, a 403 from your sandbox's proxy) **or you can't run code:** right away give the user the upload link from `request_file_upload`. They can drag the same file onto that page. Wait for them to say "done". Mention once that their Claude admin can add `mcp.pathwd.app` to the allowed domains for code execution (Settings → Capabilities) so dropped files work directly.
  4. **A small text file you can read in full** (CSV, JSON, scripted answers): `write_workspace_file` is fine.

  Every route gives the `uploads/<id>/<name>` path to use in the command. Never say a file is uploaded until the tool result, curl or the user confirms it. A command that succeeds uses the upload up: bring the file in again to use it again.
- **A file you write** (scripted answers, an HTML body, a long context text): `write_workspace_file` with a relative path, e.g. `answers/win-job.json` (up to about 1 MB). It stays in the workspace for later commands; an existing file is replaced only with `overwrite: true`.
- **Files a command saves** (`download-record-file`, exports) come back as download links in its result: give the link to the user. To read a file's text yourself, use `--stdout`, or leave out `--out` where the command prints by default.
- Standard input (`--in -`) isn't available.

### Deletes are confirmed

`delete-folder-sticker`, `reset-folder-stickers` (when it would delete the folder's own stickers or show hidden ones again), `delete-calendar`, `delete-calendar-group`, `delete-calendar-event`, `clear-appointment` (when the field is set), `delete-record-file`, `delete-message`, `delete-record-note` and `delete-custom-field` ask for confirmation. The first call deletes nothing: it answers `NOT DELETED: this needs confirmation`, says what it would delete and gives a code. Show the user exactly what will be deleted, ask, and only after an explicit yes run the SAME command again with `--confirm <code>`. Never pass `--confirm` without that yes, and never guess a code. The code works only for that exact command, account and user, and expires within 20 minutes. `--dry-run` deletes nothing and needs no code. Archiving and unlinking (`archive-record`, `archive-record-file`, `unlink-record`, `cancel-task`, `share-calendar --access remove`, `update-calendar-group --remove-calendars`, `set-filter --clear`) don't ask either.

### Access: always real-world

Every command runs as the **acting user** (`--as-user`, else the signed-in user) and only does what that user could do in the web app; there is no unrestricted mode. A refusal reads `<user> can't <action> — <why>, as in the web app.`

- Records, fields, relationships and Notes fields are shown and written only as the user's record viewer allows; business objects without View don't exist for them; tags limit which records they see.
- **`--as-user <id|email|name>`** is the web's Impersonate: account **Admins** only, never on themselves or a deactivated member.
- **`--mode super-user`** on `pathway-run start` / `execute-pathway` starts a run even when the pathway's visibility criteria don't match the record; it needs a role with the Designer permission, and every other launch rule still applies.
- Pathway runs are exempt from the business object create / edit / archive rules and the record viewer's visibility, as in the web app; their launch rules still apply.

What each action needs, why something isn't listed, and how to explain a refusal: [access-and-visibility.md](cli/access-and-visibility.md).

### Designer context (`context`)

Designers can write **context** on the account and on what they set up: business objects, fields (relationship fields too), field groups, relationships, milestones, workflow steps, tasks, stickers, folder stickers, file types, pathways and pathway steps. It is their own description of what the thing is for and how it's meant to be used. Anyone can read it.

- **The session context comes first.** On `use-account` and automatically before the first command of every session (the first after you sign in, too), the CLI gives you the session context: the account (id, name, time zone, country and its Account context, which describes the business), the account's active users with the roles each holds (every member also has All Users; for a child account, its own members), the pathways the user can start from the dashboard with their context, and the user's **process context** (what the designers wrote for the user's roles on the business objects they work with). Read it before acting. It prints before the command's output: `Account: <name> (<id>) · time zone … · country …` with the Account context under it, `Users (<n>; …)` with one `<id>  <name> <email>: <roles>` line each, `Dashboard pathways (<n>)` with their context, and `Process context (<n>)` grouped by business object. It is shown once per session: run **`session-context`** to reload it in full (for example after your context was compacted), or pass `--with-context`. Under `--as-user` it is not reloaded (you keep your own). The result that carries it also gets PATH's core ideas and the list of guides and workflows `path_docs` returns.
- **Read an object's context before acting on it.** A `get-*` command prints it as a `context:` line (`get-business-object`, `get-field`, …); a list prints it under the table (`Field context:`, `Folder context:`, … one `<name> (<id>): <context>` line each, only what has context) (`list-fields`, `pathway-run available`, `list-workflow-*`, …).
- **Folder stickers:** `list-folders` lists each folder's stickers under the table (`<folder id> "<folder>" [Custom]: <folder sticker id> "<name>" (own | inherited <business object sticker id>, hidden)`), then `Sticker context:`. A copy of a business object sticker with no context of its own shows the business object sticker's context; that context isn't the folder's, so never save it into a folder. To change a folder sticker's own context: `update-folder --folder-id <id> --data '{"sticker_context":{"<sticker name or id>":"…"}}'` (`null` clears it, and a copy shows its business object sticker's context again). Folder, folder-sticker, report and report-column context, filters and creation are only in this CLI; the Designer CLI has none of them. **Folder stickers are Standard or Custom** (`custom_stickers`, apart from the columns' `is_custom`): Standard shows the business object's stickers; Custom still shows them (each can be hidden on the folder, and Designer changes keep flowing in) and adds the folder's own. In `list-folders` an inherited sticker reads `inherited <business object sticker id>` (and `hidden` when hidden on the folder); an own sticker reads `own`. Add, change and delete own stickers with `create-folder-sticker` / `update-folder-sticker` / `delete-folder-sticker`, hide or show an inherited one with `hide-folder-sticker` / `show-folder-sticker`, go back to Standard with `reset-folder-stickers` (`delete-folder-sticker`, and a reset that would delete own stickers or show hidden ones again, ask for confirmation first: see **Deletes are confirmed**); a sticker's formula alone: `set-filter folder-sticker <folder id> --sticker <folder sticker id>`. The business object stickers themselves are Designer work (`path-designer create-sticker` / `update-sticker`).
- **Pathway runs** show the pathway's, the step's and each field's context at the screen where it matters (see the [`cli-pathways` workflow](../../pathwd-pathways/references/workflows/pathways.md)).
- **Pathways and tasks for the request.** The pathways the user can start from the dashboard come with the session context. When the request is about a particular record (any kind of record), open it: `get-record` lists `Pathways you can start on this record` (its Pathways tab, the same as `pathway-run available --record-id <id>`) and `Open tasks on this record` (the tasks already on it, each with its context and the pathway completing it runs). Read each one's `context` (what it is for) to decide whether one does what the user asked. A pathway or task is the business's own process: it fills the right fields, creates and links the right records, moves the record through its workflow and sends what should be sent. So when one fits, offer it before doing the same thing with raw edits (`create-record`, `update-record`, `run-step`); a task's pathway runs by completing the task (`pathway-run start --pathway-id <id> --record-id <id> --task-id <task id>`). Context already shown this session comes back as `(shown earlier this session)`: it is tracked, not missing.
- **A record that isn't found.** When the user means one particular record and a search finds nothing that matches, try other spellings and forms (typos, sound-alikes, part of the name, without LLC/Inc, with and without punctuation, through the related customer or address) before asking or giving up. Ask only after those come up empty, saying what you searched for.
- **Records come with the context of what they show.** `get-record` follows the record with the business object's, its fields' and its relationships' context. `list-records --folder-id` prints the records, then `Folder context` (the folder's own: what it is for, who uses it), `Business object context` and `Field context` (the columns' fields). `run-report` prints the rows, then `Report context` (the report's own), `Business object context`, `Column context`, `Relationship context (column paths)` and `Custom Field context` (filter-only Custom Fields: not shown, but the filter compares them). A report column's context is its own when the report has one on it, otherwise its field's; calculated, count and Time Between columns only have their own. `list-folders`, `list-reports` and `get-report` list each folder's / report's context under the table. `list-related-records` gives the relationship's, the related BO's and each column's context. A table column has no context of its own: it shows the context of the field (or relationship field, relationship or pathway) it is based on. A relationship field with no context of its own shows the related field's.
- **Context is given once per session.** The first time a piece of context appears in a session it is shown in full; later calls leave it out: they print `<label>: (shown earlier this session)` and one closing line. Pass **`--with-context`** to get it all again (the session context too), for example after your context was compacted, or when you are a subagent that didn't see the earlier calls. There are no exceptions: a second view of the same folder or report leaves out its folder / report, column and business object context, and a second run of the same pathway leaves out its pathway, step and field context. Only `get-account` and `session-context` (explicit requests) always show it. Context you just saved (`update-folder` / `update-report --context`) counts as shown. Context left out this way never means "no context".
- **Design metadata is fetched once per session.** Business objects, fields, field groups, relationships, milestones, workflow steps and tasks, pathways and their steps, folders, reports, stickers, calendars, users and roles are cached for the session (the same session as context), so repeating a list or lookup costs no request. Records and everything on them (tasks, files, messages, emails, history, notifications, appointments) and account settings are always live. A design change made through either CLI clears the cache, and a name or id that isn't in it is looked up again with fresh data. Changes someone else makes in the web app during the session show up after `clear-cache` (or `--no-cache` on one call).
- **Context is the designer's intent, not data.** Use it to understand what something is for, what to ask the user and what a sensible answer looks like. Never invent a value from it when the user hasn't decided.

## Commands

Each command's note has its examples, output and errors (see the top of this page). Commands that change data are marked ✎; deletes that ask for a confirmation code are marked ✎✖.

### Account and session
| Command | For |
|---------|-----|
| `list-accounts` | The accounts the user can open |
| `use-account <id>` ✎ | Save the default account for this connection and print its session context |
| `session-context` | The session context in full again: account, users and roles, dashboard pathways, process context |
| `get-account [--account-id <id>]` | One account: name, time zone, country, Account context |
| `list-users` / `list-roles` | The account's active users: name and role names only (Admin or User Admin; the name is what commands that take a user accept; `--include-inactive` adds inactive users, marked) / roles |
| `get-profile` / `update-profile` ✎ | The user's name and time zone |
| `get-notification-settings` / `update-notification-settings` ✎ | Which channels (Feed, popup, email, text, mobile) each kind of notification reaches the user by |
| `web-link record\|folder\|report <id or name>` | A web-app link |
| `clear-cache` | Fetch design metadata (business objects, fields, pathways, …) fresh after a change made elsewhere |

### How the account is set up (read-only)
| Command | For |
|---------|-----|
| `list-business-objects` / `get-business-object` | The business objects the user can view, with their context |
| `list-fields` / `get-field` | A business object's fields: ids, types, options (cut short in the list; `get-field --field-id <id>` shows every one), context |
| `list-field-groups` | Its field groups (Record Viewer sections) |
| `list-relationships` | Its relationships (links to other business objects), with ids |
| `list-workflow-milestones` / `list-workflow-steps` / `list-workflow-tasks` | Its milestones, workflow steps and task templates |
| `list-appointment-fields` | Appointment fields events can be scheduled on |
| `list-tag-groups` / `list-file-types` | Tag groups and tags; a business object's file types |
| `parse-filter` / `get-filter <target> <id>` | Check a Logic Builder formula; print any stored filter (milestone, task, folder, report, …) as a formula |

Changing any of this is Designer work.

### Records
| Command | For |
|---------|-----|
| `search --query <text>` | Quick search by name, number or other text (one page, 15 by default) |
| `find-records --bo-name <bo> --logic '<formula>'` | Records matching conditions on their values; `--count`, `--columns`, `--all`, `--group-by` with totals |
| `list-records --folder-id <id>` | A folder's records (or a business object's recently viewed ones) |
| `get-record --record-id <id>` | One record as the record viewer shows it: fields by name, milestones, linked records, open tasks, available pathways; `--raw` for stored values |
| `list-related-records --record-id <id>` | Its related lists, and one list's rows |
| `create-record` ✎ / `update-record` ✎ | Create a record / change field values (stored values: [field-values.md](cli/field-values.md)) |
| `link-record` ✎ / `unlink-record` ✎ | Link two records on a relationship / remove the link |
| `archive-record` ✎ / `unarchive-record` ✎ | Archive records (kept, out of lists) / bring one back |
| `run-step` ✎ | Record a workflow step on a record (milestones are re-checked) |

**Record links:** a link to a specific record opens it on the dashboard, always in exactly this form: `https://pathwd.app/accounts/{accountId}/dashboard?record={recordId}`, e.g. `https://pathwd.app/accounts/1234/dashboard?record=6a1f0c2e9b4d3a7e5c8f1234`. `{recordId}` is the record's `id` from `get-record` / `find-records` (`web-link record <id>` without `--tab` prints it), never a record number or name. No other form opens it: no `/record/<id>` or `/records/<id>` paths, no folder- or report-scoped record URLs, no other host, no other query parameters or `:<tab>` suffix.

### A record's activity
| Command | For |
|---------|-----|
| `list-record-files` / `download-record-file` | Its files (with types) / one file's content |
| `upload-record-file` ✎ / `update-record-file` ✎ | Add files / change a file's name, type or comment |
| `archive-record-file` ✎ / `unarchive-record-file` ✎ / `delete-record-file` ✎✖ | Archive, bring back, or delete a file (Admin or Delete Files) |
| `list-record-notes` / `add-record-note` ✎ / `update-record-note` ✎ / `delete-record-note` ✎✖ | Notes in its Notes fields (edit and delete: the author's own) |
| `list-record-messages` / `send-message` ✎ / `update-message` ✎ / `delete-message` ✎✖ | Messages on the record (`--to` @mentions and notifies) |
| `list-record-emails` / `send-record-email` ✎ | Its emails / send a real email from it (`--preview` first) |
| `list-record-tasks` | Its open tasks |
| `get-history-by-record` / `get-history-session` / `get-event-metadata` | What changed and who changed it (the [`path-record-history` guide](../../pathwd-history/references/record-history.md)) |

### Tasks, Feed and notifications
| Command | For |
|---------|-----|
| `list-user-tasks` | The user's own open tasks: a short table (id, task, record, record_id, due) in every format |
| `create-task` ✎ / `edit-task` ✎ / `assign-task` ✎ | New task (from a template or custom) / change one / reassign one |
| `complete-task` ✎ / `cancel-task` ✎ | Complete or cancel a task (a task with a pathway is completed by `pathway-run start --task-id`) |
| `list-feed` / `list-notifications` / `list-popups` | The user's Feed (one page) / notifications / popups |
| `feed-unread-counts` / `feed-records` | Unread counts per business object or record / the records the Feed mentions |
| `mark-notification-read` ✎ / `mark-all-notifications-read` ✎ / `pin-notification` ✎ | Mark read, pin or unpin the user's own items |

### Calendars
| Command | For |
|---------|-----|
| `list-calendars` / `get-calendar` / `list-calendar-groups` / `get-calendar-group` | Calendars and groups, owners and sharing |
| `list-calendar-events` / `get-calendar-event` | Events of every origin in a date window (alias `list-appointments`) / one event |
| `set-appointment` ✎ / `clear-appointment` ✎✖ | Schedule or move / clear a record's appointment field |
| `create-calendar-event` ✎ / `update-calendar-event` ✎ / `delete-calendar-event` ✎✖ | Standalone events, or link one to a record's appointment field |
| `create-calendar` ✎ / `update-calendar` ✎ / `share-calendar` ✎ / `delete-calendar` ✎✖ | Manage calendars (Admin / Calendar Admin, or the owner) |
| `create-calendar-group` ✎ / `update-calendar-group` ✎ / `share-calendar-group` ✎ / `delete-calendar-group` ✎✖ | Manage calendar groups |

### Folders and reports
| Command | For |
|---------|-----|
| `list-folders` / `list-reports` / `get-report <id>` | Folders and reports the user can open, with context; a report's columns, grouping and filter |
| `run-report --report-id <id>` | A report's rows (50 a page; `--all`), `--user-filter` |
| `create-folder` ✎ / `update-folder` ✎ | Make a folder (the [`cli-folders-skill` workflow](../../pathwd-find-records/references/workflows/folders.md)) / change its settings or context |
| `create-report` ✎ / `update-report` ✎ | Make a report, `--preview` first (the [`cli-reports-skill` workflow](../../pathwd-find-records/references/workflows/reports.md)) / change its settings or context |
| `set-filter folder\|folder-sticker\|report <id>` ✎ | Save a folder's, folder sticker's or report's filter (and user filters) |
| `create-folder-sticker` ✎ / `update-folder-sticker` ✎ / `hide-folder-sticker` ✎ / `show-folder-sticker` ✎ / `delete-folder-sticker` ✎✖ / `reset-folder-stickers` ✎✖ | A folder's own stickers, and hiding the business object's on it |
| `create-custom-field` ✎ / `update-custom-field` ✎ / `delete-custom-field` ✎✖ | A report's filter-only Custom Fields |

### Pathways
| Command | For |
|---------|-----|
| `pathway-run available` | The pathway lists, as in the web app: what the user can start from the dashboard (`--from dashboard`), a record (`--record-id <id>`), a folder or a task, with context |
| `pathway-run map` | Every step and screen of a pathway before running it, with the branches |
| `pathway-run start` ✎ / `submit` ✎ / `act` ✎ / `cancel` ✎ | Run a pathway as the web app does, answering each screen ([pathway-screens.md](../../pathwd-pathways/references/pathway-screens.md)) |
| `pathway-run show` / `list` / `log` | The paused screen again / saved runs / a run's full trace |
| `execute-pathway` ✎ | Run a pathway on the headless runner, as scheduled pathways and integrations do (best for all-automatic pathways). A pathway with screen steps runs only with `--skip-screens` (Yes/No answer No, other screens are skipped); without it nothing runs and the steps are listed |

## Examples

```
# Explore the data model
--account 1234 list-business-objects
--account 1234 list-fields --bo-name Equipment

# Browse records
--account 1234 list-folders --bo-name Equipment
--account 1234 list-records --folder-id 567
--account 1234 get-record --record-id 6a9056455ff33756afba5084

# Search
--account 1234 search --query "test"

# Create and operate on records
--account 1234 create-record --bo-name Equipment --data '{"fields":[{"field_id":20411,"value":"SN-4471"}]}'
--account 1234 execute-pathway --pathway-id 89 --record-id 6a9056455ff33756afba5084
--account 1234 send-message --record-id 6a9056455ff33756afba5084 --message "Updated via CLI"

## Commands by tool

On PATH WD Remote MCP each tool runs a group of commands: the tool takes the command, its args (the flags below) and an output format.

| Tool | Kind | Commands |
|------|------|----------|
| `path_account` | read | `list-accounts`, `use-account`, `get-account`, `session-context`, `get-profile`, `get-notification-settings`, `list-users`, `list-roles`, `web-link`, `clear-cache` |
| `path_data_model` | read | `list-business-objects`, `get-business-object`, `list-fields`, `get-field`, `list-field-groups`, `list-relationships`, `list-workflow-milestones`, `list-workflow-steps`, `list-workflow-tasks`, `list-tag-groups`, `list-file-types`, `list-appointment-fields`, `parse-filter`, `get-filter` |
| `path_find_records` | read | `search`, `find-records`, `list-records`, `get-record`, `list-related-records` |
| `path_record_activity` | read | `list-record-files`, `download-record-file`, `list-record-notes`, `list-record-messages`, `list-record-emails`, `list-record-tasks`, `get-history-by-record`, `get-history-session`, `get-event-metadata` |
| `path_my_work` | read | `list-user-tasks`, `list-notifications`, `list-feed`, `feed-unread-counts`, `feed-records`, `list-popups` |
| `path_folders_reports` | read | `list-folders`, `list-reports`, `get-report`, `run-report` |
| `path_calendars` | read | `list-calendars`, `get-calendar`, `list-calendar-groups`, `get-calendar-group`, `list-calendar-events`, `get-calendar-event` |
| `path_pathways` | read | `pathway-run available`, `pathway-run map`, `pathway-run show`, `pathway-run list`, `pathway-run log` |
| `path_create_record` | add | `create-record`, `link-record` |
| `path_add_to_record` | add | `add-record-note`, `upload-record-file`, `send-message` |
| `path_send_email` | add | `send-record-email` |
| `path_update_record` | change | `update-record`, `unarchive-record`, `unarchive-record-file`, `set-appointment`, `update-record-note`, `update-record-file`, `update-message` |
| `path_run_pathway` | change | `pathway-run start`, `pathway-run submit`, `pathway-run act`, `pathway-run cancel`, `execute-pathway`, `run-step` |
| `path_manage_tasks` | change | `create-task`, `edit-task`, `assign-task`, `complete-task` |
| `path_my_settings` | change | `mark-notification-read`, `mark-all-notifications-read`, `pin-notification`, `update-notification-settings`, `update-profile` |
| `path_manage_calendars` | change | `create-calendar`, `update-calendar`, `share-calendar`, `create-calendar-group`, `update-calendar-group`, `share-calendar-group`, `create-calendar-event`, `update-calendar-event` |
| `path_manage_folders_reports` | change | `create-folder`, `update-folder`, `create-report`, `update-report`, `set-filter`, `create-folder-sticker`, `update-folder-sticker`, `hide-folder-sticker`, `show-folder-sticker`, `create-custom-field`, `update-custom-field` |
| `path_archive_record` | remove | `archive-record`, `unlink-record` |
| `path_remove_from_record` | remove | `archive-record-file`, `delete-record-file`, `delete-record-note`, `delete-message` |
| `path_cancel_task` | remove | `cancel-task` |
| `path_delete_calendar_items` | remove | `delete-calendar`, `delete-calendar-group`, `delete-calendar-event`, `clear-appointment` |
| `path_delete_folder_report_items` | remove | `delete-folder-sticker`, `reset-folder-stickers`, `delete-custom-field` |

## Every command

| Command | Tool | What it does |
|---------|------|--------------|
| [`add-record-note`](commands/add-record-note.md) | `path_add_to_record` | Add a note to a record's Notes field, as the field's Add Note button does (posted as you, now) |
| [`archive-record`](commands/archive-record.md) | `path_archive_record` | Archive a record (or several with --record-ids) via the Archive Record step |
| [`archive-record-file`](commands/archive-record-file.md) | `path_remove_from_record` | Archive a file on a record |
| [`assign-task`](commands/assign-task.md) | `path_manage_tasks` | Assign a task on a record to a user: --user takes the name list-users shows (or an email or id); a name two users share is refused, asking for the… |
| [`cancel-task`](commands/cancel-task.md) | `path_cancel_task` | Cancel a task on a record |
| [`clear-appointment`](commands/clear-appointment.md) | `path_delete_calendar_items` | Clear a record's appointment field (deletes the calendar event that owns it) |
| [`clear-cache`](commands/clear-cache.md) | `path_account` | Clear the cached design metadata (all accounts, or one with --account); the next commands fetch it again |
| [`complete-task`](commands/complete-task.md) | `path_manage_tasks` | Mark a task as complete on a record |
| [`create-calendar`](commands/create-calendar.md) | `path_manage_calendars` | Create a calendar (Admins and Calendar Admins, as the web's Add Calendar form) |
| [`create-calendar-event`](commands/create-calendar-event.md) | `path_manage_calendars` | Create a calendar event (standalone, or a record appointment with --record/--field) |
| [`create-calendar-group`](commands/create-calendar-group.md) | `path_manage_calendars` | Create a calendar group (Admins and Calendar Admins, as the web's Add Group form) |
| [`create-custom-field`](../../pathwd-find-records/references/commands/create-custom-field.md) | `path_manage_folders_reports` | Add a filter-only Custom Field to a report: a calculation (--expression, expression syntax) or a Time Between (--from/--to: today \| field:<date field>… |
| [`create-folder`](../../pathwd-find-records/references/commands/create-folder.md) | `path_manage_folders_reports` | Create a folder with permissions (and its context: --context / --context-file) |
| [`create-folder-sticker`](../../pathwd-find-records/references/commands/create-folder-sticker.md) | `path_manage_folders_reports` | Add a sticker of the folder's own (shown on its rows when its formula matches), saved the way the folder form saves it |
| [`create-record`](commands/create-record.md) | `path_create_record` | Create a record on a business object |
| [`create-report`](../../pathwd-find-records/references/commands/create-report.md) | `path_manage_folders_reports` | Create a report the way the web report builder does: columns (fields, Relationship.Field, milestone/step dates, Milestones, Count, CALC(<expression>)… |
| [`create-task`](commands/create-task.md) | `path_manage_tasks` | Create a task on a record, like the record viewer's New Task: from a task template that the record's current milestones offer (--template; list them… |
| [`delete-calendar`](commands/delete-calendar.md) | `path_delete_calendar_items` | Delete a calendar and its events (record appointments are cleared), or move the events to another calendar first — the calendar's owner or an Admin /… |
| [`delete-calendar-event`](commands/delete-calendar-event.md) | `path_delete_calendar_items` | Delete a calendar event (clears a record appointment field) |
| [`delete-calendar-group`](commands/delete-calendar-group.md) | `path_delete_calendar_items` | Delete a calendar group (its calendars and events stay) — the group's owner or an Admin / Calendar Admin |
| [`delete-custom-field`](../../pathwd-find-records/references/commands/delete-custom-field.md) | `path_delete_folder_report_items` | Remove a report Custom Field |
| [`delete-folder-sticker`](../../pathwd-find-records/references/commands/delete-folder-sticker.md) | `path_delete_folder_report_items` | Delete one of the folder's own stickers (permanent) |
| [`delete-message`](commands/delete-message.md) | `path_remove_from_record` | Delete a message on a record (your own) |
| [`delete-record-file`](commands/delete-record-file.md) | `path_remove_from_record` | Delete a file from a record |
| [`delete-record-note`](commands/delete-record-note.md) | `path_remove_from_record` | Delete a note from a record's Notes field, as the Delete button of its Edit Note form does |
| [`download-record-file`](commands/download-record-file.md) | `path_record_activity` | Download one file attached to a record (to a workspace path, or to stdout with --stdout) |
| [`edit-task`](commands/edit-task.md) | `path_manage_tasks` | Edit an open task on a record, like a task's Edit button |
| [`execute-pathway`](../../pathwd-pathways/references/commands/execute-pathway.md) | `path_run_pathway` | Run a pathway on a record on PATH's headless runner, the way scheduled pathways and integrations run: no screens, so it suits a pathway whose steps… |
| [`feed-records`](commands/feed-records.md) | `path_my_work` | The records your Feed and popups mention (the Feed page record list) |
| [`feed-unread-counts`](commands/feed-unread-counts.md) | `path_my_work` | Unread Feed counts per business object (the menu badges) or per record |
| [`find-records`](../../pathwd-find-records/references/commands/find-records.md) | `path_find_records` | Advanced search: the records of one business object that match a Logic Builder formula (e.g. 'ISLASTWEEK([Created Date]) AND HASSTEP("Job Won")'), run… |
| [`get-account`](commands/get-account.md) | `path_account` | Show the active account (or --account-id): its name, time zone, country and context, the designers' description of the business |
| [`get-business-object`](commands/get-business-object.md) | `path_data_model` | Show one business object: names, type, identifier field, menu/messages/email switches, and its context (the designer's description of what it is for) |
| [`get-calendar`](commands/get-calendar.md) | `path_calendars` | Show a calendar: color, owner, what All Users get, and (for its owner or an Admin / Calendar Admin) who it is shared with |
| [`get-calendar-event`](commands/get-calendar-event.md) | `path_calendars` | Show one calendar event: title, when (your clock), calendar, the record and appointment field it is on, source (standalone, or the synced provider and… |
| [`get-calendar-group`](commands/get-calendar-group.md) | `path_calendars` | Show a calendar group: its calendars, owner, what All Users get, and (for its owner or an Admin / Calendar Admin) who it is shared with |
| [`get-event-metadata`](../../pathwd-history/references/commands/get-event-metadata.md) | `path_record_activity` | Get the metadata (and context) of a single history event (read-only) |
| [`get-field`](commands/get-field.md) | `path_data_model` | Show one field: type, whether a value is mandatory, whether --data can write it, group, the relationship a linked field comes from, format, default… |
| [`get-filter`](../../pathwd-find-records/references/commands/get-filter.md) | `path_data_model` | Print a stored Logic Builder filter as formula text (milestone criteria, task criteria, Fork conditions, folder/report/sticker filters, visibility… |
| [`get-history-by-record`](../../pathwd-history/references/commands/get-history-by-record.md) | `path_record_activity` | Change history for a record as a flat event table (read-only) |
| [`get-history-session`](../../pathwd-history/references/commands/get-history-session.md) | `path_record_activity` | Every event one activity session recorded — e.g. everything an inbound integration request did: the record it created, fields set, pathway steps… |
| [`get-notification-settings`](commands/get-notification-settings.md) | `path_account` | Your notification settings: account-wide rows (task, feed, …), or with --resolved every notification that can reach you and which channels you may… |
| [`get-profile`](commands/get-profile.md) | `path_account` | Show your profile settings: name, email, phone, time zone and muted report emails |
| [`get-record`](commands/get-record.md) | `path_find_records` | Get a single record by ID, as the record viewer shows it to you: field names with display values under their field groups, related records with their… |
| [`get-report`](../../pathwd-find-records/references/commands/get-report.md) | `path_folders_reports` | Show a report: its business object and type, its columns (column id, name, type, hidden, the COLUMN("name") a filter compares, a computed column's… |
| [`hide-folder-sticker`](../../pathwd-find-records/references/commands/hide-folder-sticker.md) | `path_manage_folders_reports` | Hide an inherited business object sticker on this folder (it keeps showing on the business object's other folders) |
| [`link-record`](commands/link-record.md) | `path_create_record` | Link two records via a relationship |
| [`list-accounts`](commands/list-accounts.md) | `path_account` | List your accounts (not the ones you've been deactivated in) |
| [`list-appointment-fields`](commands/list-appointment-fields.md) | `path_data_model` | List the appointment fields calendar events can be scheduled on |
| [`list-business-objects`](commands/list-business-objects.md) | `path_data_model` | List business objects (id, name, plural), then each one's context: what the designer says it is for |
| [`list-calendar-events`](commands/list-calendar-events.md) | `path_calendars` | List calendar events (all origins) in a date window: id, when (your clock; all-day events as days), calendar, from (the record id of a record… |
| [`list-calendar-groups`](commands/list-calendar-groups.md) | `path_calendars` | List the calendar groups you can see, with their calendars and owner |
| [`list-calendars`](commands/list-calendars.md) | `path_calendars` | List calendars: id, name, synced (google / outlook when merged with one), owner. get-calendar <id> shows who it is shared with |
| [`list-feed`](commands/list-feed.md) | `path_my_work` | List your dashboard Feed (newest first), like the web Feed page |
| [`list-field-groups`](commands/list-field-groups.md) | `path_data_model` | List a business object's field groups (id, name), then each one's context |
| [`list-fields`](commands/list-fields.md) | `path_data_model` | List a business object's fields: id, name, type, whether a value is mandatory (req), the relationship a linked field comes from, and selection… |
| [`list-file-types`](commands/list-file-types.md) | `path_data_model` | List a business object's file types, as Designer → Files → File Types lists them (the system types Archived and Email Attachment are left out unless… |
| [`list-folders`](../../pathwd-find-records/references/commands/list-folders.md) | `path_folders_reports` | List the folders in the user's folder menu: id, business object, name |
| [`list-notifications`](commands/list-notifications.md) | `path_my_work` | List notifications |
| [`list-popups`](commands/list-popups.md) | `path_my_work` | List your popup notifications (the in-app popups) |
| [`list-record-emails`](commands/list-record-emails.md) | `path_record_activity` | List emails on a record (newest first); --email-id shows one email in full, body included |
| [`list-record-files`](commands/list-record-files.md) | `path_record_activity` | List every file attached to a record (optionally only the given file types), with each file's type names |
| [`list-record-messages`](commands/list-record-messages.md) | `path_record_activity` | List the messages on a record, newest first (deleted messages left out) |
| [`list-record-notes`](commands/list-record-notes.md) | `path_record_activity` | List a record's Notes fields and every note in them, newest first, as the record viewer shows them: author, when (your clock), note id, and whether… |
| [`list-record-tasks`](commands/list-record-tasks.md) | `path_record_activity` | List tasks on a record |
| [`list-records`](commands/list-records.md) | `path_find_records` | List one business object's records: the ones you viewed recently (--bo-name / --bo-id alone), or a folder's (--folder-name with --bo-name / --bo-id… |
| [`list-related-records`](commands/list-related-records.md) | `path_find_records` | A record's related records as the record viewer shows them to you |
| [`list-relationships`](commands/list-relationships.md) | `path_data_model` | List a business object's relationships (id, name, the business object it links to), then each one's context |
| [`list-reports`](../../pathwd-find-records/references/commands/list-reports.md) | `path_folders_reports` | List the reports the user can open: id, business object, grouped, name |
| [`list-roles`](commands/list-roles.md) | `path_account` | List roles |
| [`list-tag-groups`](commands/list-tag-groups.md) | `path_data_model` | List tag groups and their active tags |
| [`list-user-tasks`](commands/list-user-tasks.md) | `path_my_work` | Your open tasks (the dashboard's task list), as a short table: id, task, record ("Job 1347"), record_id, due (your time zone), soonest due first |
| [`list-users`](commands/list-users.md) | `path_account` | The account's active users: each one's name and role names, nothing else (every user also has All Users) |
| [`list-workflow-milestones`](commands/list-workflow-milestones.md) | `path_data_model` | List a business object's workflow milestones (id, name, initial/disabled, the workflow tasks it creates or offers, what runs on entry), then each… |
| [`list-workflow-steps`](commands/list-workflow-steps.md) | `path_data_model` | List a business object's workflow steps (id, name, initial/disabled), then each one's context |
| [`list-workflow-tasks`](commands/list-workflow-tasks.md) | `path_data_model` | List a business object's workflow tasks (id, title, pathway, assignment, stored rules), then each one's context |
| [`mark-all-notifications-read`](commands/mark-all-notifications-read.md) | `path_my_settings` | Mark every unread Feed item read (the Feed page "Mark all as read"), or one record's with --record-id |
| [`mark-notification-read`](commands/mark-notification-read.md) | `path_my_settings` | Mark one of your Feed items (or with --popup, a popup) as read |
| [`parse-filter`](../../pathwd-find-records/references/commands/parse-filter.md) | `path_data_model` | Validate a Logic Builder formula (e.g. '[Department] = "Retail" AND INMILESTONE("Lead")') against a business object without saving it |
| [`pathway-run act`](../../pathwd-pathways/references/commands/pathway-run-act.md) | `path_run_pathway` | A non-advancing action on the paused screen, e.g. a lookup search: --set search=Acme [--set page=2] |
| [`pathway-run available`](../../pathwd-pathways/references/commands/pathway-run-available.md) | `path_pathways` | Which pathways a user could start from a launch point (a record's Pathways tab, the dashboard, a folder or a task), each with its designer context… |
| [`pathway-run cancel`](../../pathwd-pathways/references/commands/pathway-run-cancel.md) | `path_run_pathway` | Cancel a paused run (like closing the web modal) |
| [`pathway-run list`](../../pathwd-pathways/references/commands/pathway-run-list.md) | `path_pathways` | List saved pathway runs, newest first |
| [`pathway-run log`](../../pathwd-pathways/references/commands/pathway-run-log.md) | `path_pathways` | A run's trace: steps, busy messages, every server request and reply |
| [`pathway-run map`](../../pathwd-pathways/references/commands/pathway-run-map.md) | `path_pathways` | Map a pathway before running it (read-only, nothing runs): every step in order with what its screen shows and asks for (fields, choices, question… |
| [`pathway-run show`](../../pathwd-pathways/references/commands/pathway-run-show.md) | `path_pathways` | Show a run: the screen it is waiting on, or how it ended |
| [`pathway-run start`](../../pathwd-pathways/references/commands/pathway-run-start.md) | `path_run_pathway` | Start a pathway run, exactly as the web app runs it; stops at the first screen that needs input |
| [`pathway-run submit`](../../pathwd-pathways/references/commands/pathway-run-submit.md) | `path_run_pathway` | Answer the paused screen (only what that screen allows) and continue the run |
| [`pin-notification`](commands/pin-notification.md) | `path_my_settings` | Pin (or with --unpin, unpin) one of your Feed items |
| [`reset-folder-stickers`](../../pathwd-find-records/references/commands/reset-folder-stickers.md) | `path_delete_folder_report_items` | Put a folder's stickers back to Standard: only its business object's stickers, all shown |
| [`run-report`](../../pathwd-find-records/references/commands/run-report.md) | `path_folders_reports` | Run a report and print its rows (the web app's report view; in real-world mode only reports the acting user can open): a table of record (the record… |
| [`run-step`](../../pathwd-pathways/references/commands/run-step.md) | `path_run_pathway` | Run a workflow step on a record (triggers milestone evaluation) |
| [`search`](commands/search.md) | `path_find_records` | Search records globally, like the web's search (archived records left out unless --include-archived) |
| [`send-message`](commands/send-message.md) | `path_add_to_record` | Post a message on a record |
| [`send-record-email`](commands/send-record-email.md) | `path_send_email` | Send a real email from a record, as the record viewer's Email tab Compose → Send does: it goes out from you (your name and PATH email address), the… |
| [`session-context`](commands/session-context.md) | `path_account` | Show the session context in full: the account and its context, its users and their roles, the pathways you can start from the dashboard with their… |
| [`set-appointment`](commands/set-appointment.md) | `path_update_record` | Schedule or change a record's appointment field (creates or updates the calendar event that owns it) |
| [`set-filter`](../../pathwd-find-records/references/commands/set-filter.md) | `path_manage_folders_reports` | Replace a stored Logic Builder filter from formula text |
| [`share-calendar`](commands/share-calendar.md) | `path_manage_calendars` | Share a calendar with users and/or roles (view or edit its events), make one user its owner, or remove their access — the calendar's owner or an Admin… |
| [`share-calendar-group`](commands/share-calendar-group.md) | `path_manage_calendars` | Share a calendar group with users and/or roles (view, or edit its name and calendars), make one user its owner, or remove their access — the group's… |
| [`show-folder-sticker`](../../pathwd-find-records/references/commands/show-folder-sticker.md) | `path_manage_folders_reports` | Show again an inherited business object sticker hidden on this folder |
| [`unarchive-record`](commands/unarchive-record.md) | `path_update_record` | Unarchive a record |
| [`unarchive-record-file`](commands/unarchive-record-file.md) | `path_update_record` | Unarchive a file on a record |
| [`unlink-record`](commands/unlink-record.md) | `path_archive_record` | Unlink a record from its target on a given relationship |
| [`update-calendar`](commands/update-calendar.md) | `path_manage_calendars` | Rename or recolor a calendar, or set what All Users get (view, edit or none) — the calendar's owner or an Admin / Calendar Admin |
| [`update-calendar-event`](commands/update-calendar-event.md) | `path_manage_calendars` | Update a calendar event, or link/unlink it to a record appointment field |
| [`update-calendar-group`](commands/update-calendar-group.md) | `path_manage_calendars` | Rename a calendar group or change its calendars (Edit on the group), or set what All Users get (the group's owner or an Admin / Calendar Admin) |
| [`update-custom-field`](../../pathwd-find-records/references/commands/update-custom-field.md) | `path_manage_folders_reports` | Change a report Custom Field's name, definition (--expression, or --from/--to) or context (--context / --context-file) |
| [`update-folder`](../../pathwd-find-records/references/commands/update-folder.md) | `path_manage_folders_reports` | Change a folder's context or settings, saved the way the folder form saves it (everything else as stored) |
| [`update-folder-sticker`](../../pathwd-find-records/references/commands/update-folder-sticker.md) | `path_manage_folders_reports` | Change one of the folder's own stickers: name, icon, color, formula and/or context (the rest as stored) |
| [`update-message`](commands/update-message.md) | `path_update_record` | Update a message on a record |
| [`update-notification-settings`](commands/update-notification-settings.md) | `path_my_settings` | Turn your notification channels on or off, or change your notification phone number |
| [`update-profile`](commands/update-profile.md) | `path_my_settings` | Change your name or time zone, the way the profile settings modal saves them |
| [`update-record`](commands/update-record.md) | `path_update_record` | Update a record by ID |
| [`update-record-file`](commands/update-record-file.md) | `path_update_record` | Update a file on a record |
| [`update-record-note`](commands/update-record-note.md) | `path_update_record` | Change the text of a note in a record's Notes field, as its Edit Note form does |
| [`update-report`](../../pathwd-find-records/references/commands/update-report.md) | `path_manage_folders_reports` | Change a report's context or settings, saved the way the report form saves it (columns, filter, scheduling as stored) |
| [`upload-record-file`](commands/upload-record-file.md) | `path_add_to_record` | Upload one or more workspace files to a record |
| [`use-account`](commands/use-account.md) | `path_account` | Set the default account for subsequent commands (kept for this connection), and show its session context: the account and its context (what the… |
| [`web-link`](commands/web-link.md) | `path_account` | Print the PATH web app link to a record, folder or report (the same link the app opens and shares) |
