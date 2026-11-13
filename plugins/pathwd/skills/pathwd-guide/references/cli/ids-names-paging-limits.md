# Ids, names, accounts, paging and limits

How PATH CLI commands name things (an id or a name), which account a command runs in, how much a list command returns by default, the output formats, and the limits commands run under on PATH WD Remote MCP. The paging and flag tables are generated from the CLI's own definitions.

## Ids and names

**A record id is 24 hexadecimal characters** (`6a9056455ff33756afba5084`). It is the `id` in `get-record`, `search`, `find-records`, `list-records`, `run-report` and `list-related-records` output. Record files (`id`; the stored `file_id` is a UUID), notes, messages and calendar events have 24-character ids too. Business objects, fields, relationships, folders, reports, pathways, steps, milestones, tasks, users, roles, calendars and file types have numeric ids.

**A record's identifier or number is not its id.** "Job 26-0074", "Smith Plumbing" or "PO 1182" is what the record viewer shows as the record's title (the `record` key in `get-record`, `identifier` in related lists). A command that takes `--record-id` needs the 24-character id: find it with `search --query "26-0074"` (quick search, by name or number) or `find-records` (by field values). A record number passed as `--record-id` fails with `ERROR [404]: Not Found`.

**Names are matched exactly, ignoring case** — never partially. A name that matches nothing fails with `<Kind> "<name>" not found — did you mean "…"?` and up to three close names (with nothing close, the command that lists them); two things with the same name fail with `Ambiguous … Matches: …` listing their ids, and the id form of the flag then picks one.

| Thing | By name | Notes |
|---|---|---|
| Business object | `--bo-name Job` or `--bo-name Jobs` | singular or plural name; `--bo-id` takes the id |
| Folder | `--folder-name "Active Jobs"` | together with `--bo-name` / `--bo-id` (folder names repeat across business objects) |
| Report | `--report-name "Won this month"` | `run-report`, `update-report` |
| Pathway | `--pathway-name "Record Deposit"` | `pathway-run start`, `pathway-run map`, `execute-pathway` |
| Field | `--field "Install Date"` (Notes and appointment fields); `pathway-run submit --set "<Field name>=…"` | `create-record` / `update-record` `--data` takes only numeric `field_id`s (the `id` column of `list-fields --bo-name <bo>`) |
| Relationship | `list-related-records --relationship-name` | `link-record` / `unlink-record` take only `--relationship-id` (`list-relationships --bo-name <bo>`) |
| User | `--user`, `--assign-to`, `--to`, `--users`, `--as-user`: the name `list-users` shows, an email or an id | A name two users share is refused, asking for the email. `assign-task --user-id` takes only the id; with `create-folder` / `create-report --users` an unknown name stops the command before anything is created (`User "…" not found — did you mean "…"? No folder was created.`) |
| Role | `--roles`: names (calendars: ids or names) | with `create-folder` / `create-report` an unknown role stops the command before anything is created (`Role "…" not found — did you mean "…"? No report was created.`) |
| Calendar | `--calendar Installs` | id or name |
| File type | `--file-type Contract` | id or name |
| Task template | `create-task --template "Call customer"` | id or title |

Every flag that names an existing thing, from the CLI's definitions:

<!-- generated:id-name-flags -->
**Ids only**

| Flag | Commands |
|---|---|
| `--account-id <id>` | `get-account` |
| `--bo <id>` | `get-filter`, `parse-filter`, `set-filter` |
| `--bo-id <id>` | `archive-record`, `create-folder`, `create-folder-sticker`, `create-record`, `create-report`, `delete-folder-sticker`, `find-records`, `get-business-object` and 19 more |
| `--calendar-ids <ids>` | `list-calendar-events` |
| `--email-id <id>` | `list-record-emails` |
| `--event-id <id>` | `get-event-metadata` |
| `--field-id <id>` | `get-field`, `get-record` |
| `--file-id <id>` | `archive-record-file`, `delete-record-file`, `download-record-file`, `unarchive-record-file`, `update-record-file` |
| `--folder-id <id>` | `create-folder-sticker`, `delete-folder-sticker`, `hide-folder-sticker`, `list-records`, `pathway-run available`, `pathway-run start`, `reset-folder-stickers`, `show-folder-sticker` and 2 more |
| `--message-id <id>` | `delete-message`, `update-message` |
| `--note-id <id>` | `delete-record-note`, `update-record-note` |
| `--pathway <id>` | `get-filter`, `set-filter` |
| `--pathway-id <id>` | `execute-pathway`, `pathway-run map`, `pathway-run start` |
| `--record <id>` | `create-calendar-event`, `update-calendar-event` |
| `--record-id <id>` | `add-record-note`, `archive-record`, `archive-record-file`, `assign-task`, `cancel-task`, `complete-task`, `create-task`, `delete-message` and 31 more |
| `--record-ids <ids>` | `archive-record` |
| `--related-record-id <id>` | `link-record` |
| `--relationship-id <id>` | `link-record`, `list-related-records`, `unlink-record` |
| `--report-id <id>` | `parse-filter`, `run-report`, `update-report` |
| `--run <id>` | `pathway-run act`, `pathway-run cancel`, `pathway-run log`, `pathway-run show`, `pathway-run submit` |
| `--step-id <id>` | `run-step` |
| `--sticker <id>` | `get-filter`, `set-filter` |
| `--task-id <id>` | `assign-task`, `cancel-task`, `complete-task`, `edit-task`, `pathway-run available`, `pathway-run start` |
| `--user-id <id>` | `assign-task` |

**Names accepted** (the flag's own help says which forms)

| Flag | Takes | Commands |
|---|---|---|
| `--as-user <id\|email\|name>` | Check for another account member (admin impersonation session) | `pathway-run available` |
| `--as-user <id\|email\|name>` | Map as another account member (admin impersonation session) | `pathway-run map` |
| `--as-user <id\|email\|name>` | Run as another account member (admin impersonation session) | `pathway-run start` |
| `--assign-to <user>` | Assignee: an active user's id, email or full name; "none" to unassign | `create-task`, `edit-task` |
| `--bo <id\|name>` | Business object, to pick a folder or report name that several share | `web-link` |
| `--bo <id\|name>` | Only items on this business object | `list-feed` |
| `--bo <id\|name>` | Only records of this business object | `feed-records` |
| `--bo-name <name>` | Business object name | `get-business-object`, `get-field`, `list-field-groups`, `list-fields`, `list-relationships`, `list-workflow-milestones`, `list-workflow-steps`, `list-workflow-tasks` |
| `--bo-name <name>` | Business object name (for --folder-name) | `create-folder-sticker`, `delete-folder-sticker`, `hide-folder-sticker`, `reset-folder-stickers`, `show-folder-sticker`, `update-folder`, `update-folder-sticker` |
| `--bo-name <name>` | Business object name (required unless --bo-id or --folder-id) | `list-records` |
| `--bo-name <name>` | Business object name (resolved to ID) | `create-folder`, `create-record`, `create-report`, `find-records`, `list-file-types`, `parse-filter`, `run-step` |
| `--bo-name <name>` | Filter by business object name | `list-folders` |
| `--bo-name <name>` | Filter by business object name (resolved to ID) | `search` |
| `--bo-name <name>` | Only this business object (by name) | `list-appointment-fields` |
| `--calendar <id\|name>` | Calendar | `create-calendar-event`, `set-appointment`, `update-calendar-event` |
| `--field <id\|name>` | Appointment field on the record | `clear-appointment`, `set-appointment` |
| `--field <id\|name>` | Only this Notes field | `list-record-notes` |
| `--field <id\|name>` | The appointment field (default: the record has only one) | `create-calendar-event`, `update-calendar-event` |
| `--field <id\|name>` | The Notes field (optional when the record has only one) | `add-record-note` |
| `--field-name <name>` | Field name (case-insensitive) | `get-field` |
| `--file-type <id-or-name>` | File type for the upload(s): an id or a name from list-file-types on the record's BO | `upload-record-file` |
| `--file-type <ids-or-names>` | Only files carrying one of these file types (comma-separated ids and/or names, names case-insensitive) | `list-record-files` |
| `--folder-name <name>` | Folder name (requires --bo-id or --bo-name) | `create-folder-sticker`, `delete-folder-sticker`, `hide-folder-sticker`, `reset-folder-stickers`, `show-folder-sticker`, `update-folder`, `update-folder-sticker` |
| `--folder-name <name>` | Folder name on the business object | `list-records` |
| `--map-relationship <name-or-id>` | Relationship (name or ID) from this folder's BO to the account's Address BO; sets map_enabled and map_relationship_id | `create-folder` |
| `--move-events-to <calendar>` | Keep the events: move every one to this calendar (one you can edit); record appointments follow | `delete-calendar` |
| `--pathway-name <name>` | Pathway name (exact, case-insensitive) | `pathway-run map` |
| `--pathway-name <name>` | Pathway name (resolved to ID) | `execute-pathway`, `pathway-run start` |
| `--record-file <ids-or-names>` | Attach files already on the record: list-record-files id, file_id or file name, comma-separated (repeatable) | `send-record-email` |
| `--relationship-name <name>` | The relationship by name or inverse tab name (case-insensitive) | `list-related-records` |
| `--report-name <name>` | Report name (resolved to ID) | `run-report`, `update-report` |
| `--roles <list>` | Comma-separated roles: ids or names ("All Users" sets what everyone gets) | `share-calendar`, `share-calendar-group` |
| `--roles <names>` | Comma-separated role names | `create-folder` |
| `--roles <names>` | Share with these roles (comma-separated) | `create-report` |
| `--sticker <id-or-name>` | The folder sticker: its id (list-folders) or name; an inherited one also by its business object sticker id | `delete-folder-sticker`, `hide-folder-sticker`, `show-folder-sticker`, `update-folder-sticker` |
| `--template <id\|title>` | A task template offered on this record (see --list-templates) | `create-task` |
| `--to <users>` | Comma-separated users to @mention and notify, as the web's message box does: ids, emails or full names | `send-message` |
| `--users <list>` | Comma-separated users: ids, emails or full names | `share-calendar`, `share-calendar-group` |
| `--users <names>` | Comma-separated users: names as list-users shows them, or emails | `create-folder` |
| `--users <names>` | Share with these users (comma-separated: names as list-users shows them, or emails) | `create-report` |
<!-- /generated:id-name-flags -->

## Accounts

- **Ids belong to one account.** A record, business object, field, pathway or user id from one account means nothing in another; a "not found" or 404 on an id that worked before usually means the command ran in a different account.
- **Which account a command uses:** `--account <id>` on that command, else the account saved with `use-account <id>`. Without either, account commands fail with `Missing --account <id>. Run "path list-accounts" to see available accounts.` (the account list is `list-accounts`).
- `get-account --account-id <id>` reads one account's details; it doesn't change which account other commands use.
- On PATH WD Remote MCP the saved account belongs to the connection, so every chat the user has with it shares one saved account: `use-account` in one chat changes it for the others. A command that compares or copies between accounts passes `--account` on every call.
- `list-users` lists the account's active users by name with their role names (Admins and User Admins; `--include-inactive` adds the inactive users, marked inactive); the name is what every command that takes a user accepts, and an email works too. Access rules (roles, tags) are per account, so the same person can see different things in two accounts.

## Paging

Some list commands return **one page** by default (a full page can mean there are more); others fetch **every page** and return the whole list. The page size is the default of `--per-page` (or `--limit`).

<!-- generated:paging -->
| Command | Returns by default | Page size | Every row |
|---|---|---|---|
| `create-report` | **one page** (`--preview`) | 50 (`--per-page`) | `--all` |
| `find-records` | **one page** | 25 (`--per-page`) | `--all` |
| `get-history-by-record` | every page | 100 (`--limit`) | the default (`--page <n>` gives one page) |
| `list-calendar-events` | **one page** (search only) | 25 (`--per-page`) | `--page` 2, 3, … (no `--all`) |
| `list-feed` | **one page** | 25 (`--per-page`) | `--page` 2, 3, … (no `--all`) |
| `list-fields` | every page | 100 (`--per-page`) | the default (`--page <n>` gives one page) |
| `list-folders` | every page | 50 (`--per-page`) | the default (`--page <n>` gives one page) |
| `list-notifications` | **one page** | 15 (`--per-page`) | `--page` 2, 3, … (no `--all`) |
| `list-popups` | **one page** | 25 (`--per-page`) | `--page` 2, 3, … (no `--all`) |
| `list-record-emails` | every page | 15 (`--per-page`) | the default (`--page <n>` gives one page) |
| `list-record-messages` | every page | 15 (`--per-page`) | the default (`--page <n>` gives one page) |
| `list-records` | every page (folders only) | 30 (`--per-page`) | the default (`--page <n>` gives one page) |
| `list-relationships` | every page | 50 (`--per-page`) | the default (`--page <n>` gives one page) |
| `list-reports` | every page | 50 (`--per-page`) | the default (`--page <n>` gives one page) |
| `list-roles` | every page | 50 (`--per-page`) | the default (`--page <n>` gives one page) |
| `pathway-run list` | the newest 20 | 20 (`--limit`) | — |
| `run-report` | **one page** | 50 (`--per-page`) | `--all` |
| `search` | **one page** | 15 (`--per-page`) | `--page` 2, 3, … (no `--all`) |
<!-- /generated:paging -->

- `search` has no `--all` and no total. When the first page is full (15 by default), there may be more: ask for `--page 2`, a bigger `--per-page`, or narrow the query; for "every Job where …" use `find-records --all`.
- `find-records --count` returns only the number of matches; `find-records --all` and `run-report --all` return every row in one call (watch the output size, below).
- `list-records` pages through a folder automatically (every record in it); without a folder it lists the business object's recently viewed records only.
- Commands with no paging flags (`list-user-tasks`, `list-users`, `list-record-tasks`, `list-record-files`, `list-record-notes`, `pathway-run available`, `list-calendars`, …) aren't in the table. `list-user-tasks` and `list-users` return all their rows, but short ones: the same few columns in every format, JSON included (a busy user's tasks or a large account's users are a few kilobytes).

## Output formats

| Format | How | What it is |
|---|---|---|
| Text | the default | compact text: a list is an ASCII table (`id \| name \| …`, id first, long cells cut with `…`, a footer such as `[12 items]` or `[25 of 140 records — --page 2 for more]`, designer context under the table once per session); one item is `key: value` lines; a change is a line or two naming what changed with its ids. Each command note's **Output** shows a sample and what to read in it |
| Ids only | `format: quiet` (`--quiet`) | one id per line |
| Chosen columns | `--fields id,name` | only those table columns, by header |

`get-record` shows the record as its record viewer does (field names and display values on the user's clock); `--raw` gives the stored record instead (a header with milestones, steps and links by id, then an `id | name | value` table of field ids and stored values: UTC times, percent as a fraction), and `get-record --raw --field-id <id>` prints one stored value whole.

Notes a command prints beside its output (a count of what was left out, a warning) come after the output in the same result; a failed command's result reads `Error (exit <code>): <message>`.

## Limits on PATH WD Remote MCP

| Limit | Value | When it's reached |
|---|---|---|
| Commands per connection | 60 a minute (180 a minute across all of one user's connections) | `Too many commands: slow down (per-connection limit).` — the command didn't run; wait a little and repeat it |
| Commands at once | one at a time per connection: a later one waits up to 30 seconds for the one before | `Another command on this connection is still running. Wait for it to finish, then try again.`; when the whole service is full, `The server is busy; try again in a moment.` |
| Output | 1,000,000 bytes per command | `ERROR: the command produced more than 1000000 bytes of output and was stopped. Narrow it down (--fields, --quiet, a page size).` |
| Time | 120 seconds per command; 600 seconds for `pathway-run start` / `pathway-run submit` with `--answers` | `ERROR: the command took longer than 120s and was stopped. It may have partly completed; check the current state before retrying.` |
| Upload | 100 MB per file; 200 uploads an hour per connection | `Too many uploads this hour.` |
| `write_workspace_file` | about 1 MB (1024 KB) of content | bigger files come in through `request_file_upload` |
| Download links | need the PATH sign-in; work for 24 hours | — |
| Workspace | an upload is kept 24 hours and used up by the first write that succeeds with it (a read, a `--preview`, a `--dry-run` or a failed run leaves it); other workspace files and CLI state (saved pathway runs and their files) stay while the connection is used (7 days after its last command), files over 2 MB included; a file over 100 MB isn't kept, and the command's result says so | `"…" is no longer available (uploads are kept for 24 hours). Request a new upload.` |

- **Bulk work** (an `update-record` per record, a note on each of 40 records) is one command per record, so 60 records take at least a minute. A command refused by the rate limit changed nothing and is repeated after a pause; report progress record by record.
- A timeout doesn't undo what the command did before it stopped: check the record (or `pathway-run show`) before running it again.

## Related

- [errors.md](errors.md): every kind of error message and what to do.
- [access-and-visibility.md](access-and-visibility.md): why something isn't listed or an action is refused.
- [recipes.md](recipes.md): multi-command tasks (bulk updates, catching up on a record).
- [field-values.md](field-values.md): value forms for `--data` and pathway screens.
