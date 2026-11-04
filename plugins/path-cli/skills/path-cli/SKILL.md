---
name: path-cli
description: "PATH CLI command reference for the path_cli tool (the PATH user app). Use this skill whenever you need the exact syntax, flags, or available subcommands of a PATH CLI command before calling path_cli, or an overview of what it can do: records, folders, reports, pathway runs, tasks, notifications, calendars, files, Notes fields, history. Designer work is done in PATH's Designer."
---

> **How to run commands:** every command in this skill is a PATH CLI command string. Run it by calling the `path_cli` tool with the string as `command`, for example `path_cli(command: "list-business-objects")`. Never run these commands in a terminal. If the user isn't signed in yet, run the `path-session` skill first.

The PATH CLI is a compact client for the PATH API. The `path_cli` tool runs it and returns its output: full JSON by default, a compact table with `format: "compact"`, or IDs only with `format: "quiet"`. Prefer `compact` for lists you only need to read; it uses far fewer tokens.

This page covers the commands used most often. For **every** command with its exact flags, read the `path://command-reference` resource, or run `<command> --help` through `path_cli`, e.g. `path_cli(command: "update-record --help")`.

## Designer work: PATH's Designer

This plugin is the PATH **user app**. Everything that builds or changes how an account is set up is Designer work, done by an Admin or Designer in **PATH's Designer** (or with the PATH Designer plugin and its `path_designer` tool, if it's installed): business objects, fields, expressions, field groups, relationships, stickers, settings, workflow milestones/steps/tasks, notification rules and actions, file types, document templates, imports and exports, pathways and steps, integrations, account settings, users/roles/tags, and Designer history. Users, roles and tags also need Admin or User Admin. If the PATH Designer plugin is installed, it shares this plugin's sign-in and its skills include `path-workflow-builder`, `path-pathway-builder`, `path-expression-syntax`, `path-actions`, `path-document-templates`, `path-account-settings`, `path-account-copy`, `path-users` and `cli-bo-builder`. A command that is not in this plugin's command reference is Designer work.

## Skills for common tasks

Most requests have a skill or command with the right steps and checks. Use it instead of composing commands from this table alone:

| Task | Use |
|------|-----|
| Sign in, switch account, sign out | the `path-session` skill |
| What a PATH or PATH WD term, business object, process or pathway screen means | the `path-concepts` skill |
| Find, create, update, link, archive records | the `cli-records` command |
| Messages, emails, files and tasks on a record | the `cli-record-activity` command |
| What changed on a record, or what a user did | the `path-record-history` skill |
| Read-only tour of how an account is set up | the `cli-workflow-inspect` command |
| Running pathways (find, list, available, run, test) | the `cli-pathways` command |
| Folders | the `cli-folders-skill` command |
| Reports (build one from a plain-language ask, run it, fix it) | the `cli-reports-skill` command |
| Folder and report filters (Logic Builder formulas) | the `cli-logic-builder` command and the `path-logic-formula-syntax` skill |
| Why a record is or isn't in a milestone | the `path-milestone-explain` skill |
| Calendars, appointments, notes | the `cli-scheduling` command |
| A user's own tasks and notifications | the `cli-tasks-notifications` command |
| Building or changing business objects, fields, workflow, pathways, actions, templates, settings, users | PATH's Designer (Admin or Designer) |

## Session Workflow

Use the `path-session` skill to start a session. In short:

1. **Sign in** — call the `authenticate` tool (browser sign-in; the password is never typed in chat), then `auth_status` to confirm and list accounts
2. **Select default account (optional)** — `use-account <id>` so `--account` becomes optional on later commands
3. **Operate** — pass `--account <id>` per command, or rely on the saved default

## Usage

```
# Every command string follows this pattern:
[global-opts] <command> [command-opts]

# Global options (before the command):
#   --account <id>     Account ID (overrides PATH_ACCOUNT_ID env var / saved default)
#   --json             Full JSON output
#   --fields <list>    Comma-separated field selection for table output
#   --quiet            IDs only (one per line)
#   --as-user <user>   Act as another member of the account (id, email or name; account Admins only)
#   --with-context     Repeat designer context (and the session context) already shown this session
```

### Access: always real-world

Every command runs as the **acting user** (`--as-user`, else you) and only does what that user could do in the web app. There is no "unrestricted" mode, no global `--mode` option and no `mode` command. A refusal reads `<user> can't <action> — <why>, as in the web app.`

- **Super-user, per pathway run:** `--mode super-user` on `pathway-run start` or `execute-pathway` (or `"mode": "super-user"` in a `pathway-run test` scenario) lets that run start even when the pathway's visibility criteria don't match the record. Every other launch rule still applies (role permission, shown on records, starting business object, record access, runnable). Only users with full Admin or Designer permission may use it.
- **`--as-user <id|email|name>`** is the web's Impersonate. Only account **Admins** (top-level Admin role) can use it, never on yourself or a deactivated user.

| Area | Real-world rule |
|------|-----------------|
| Business objects | Only BOs with View exist (listings, metadata reads, records, related records, tasks, feed) |
| Search | Only BOs that are viewable **and** shown in the menu (the web search picker); `--bo-id` on any other BO is refused |
| Reading records | `get-record` / `list-records` / `list-related-records` / `search` show only what the record viewer shows: hidden fields, fields failing their user/role/logic visibility, fields in hidden field groups, hidden relationships and relationships to unviewable BOs are left out (a note on stderr says how many). Created / Modified / Last Accessed dates always show. `get-record` answers like the record viewer: field **names** with **display** values (selection text, related records by identifier, formatted numbers / currency), dates and times on the acting user's clock (all-day: account-calendar day) — in the terminal and as the plugin tool's default JSON. Explicit `--json` (tool `format: "json"`), `--raw`, `--fields` or `--quiet` give the stored record (field ids, UTC) |
| Creating / editing records | BO **Edit**; only fields visible in the record viewer. Linking / unlinking: BO Edit + the relationship visible in the record viewer |
| Archiving | BO **Archive** (or the Delete Records role permission) |
| Record tabs | Files, messages, emails, tasks, history: the record's BO must be viewable. Edit/delete a message: only your own. Notes fields: only those the record viewer shows; add a note needs BO **Edit**, edit/delete a note only your own. Delete a file: Admin or Delete Files |
| Folders / reports | Create a folder: Create Folders on its BO; create a report: Create Reports on its BO (you become its owner). A folder's or report's own filter / custom fields: Edit on that folder / report (folder / report admins: all) |
| History | `get-history-by-record` and `get-event-metadata` on a record event: the record must open as the acting user (tag visibility) and its BO be viewable. The other-record sessions a record's history carries leave out sessions on records they can't view, with a count on stderr. `get-history-session`: Admin, Designer or User Admin, else the session's record (with none, every record its events are on) must open as them. Times show on the acting user's clock; `--json` stays UTC |
| Calendar | Create / change / delete an event: Edit on its calendar (grant, owner, or the calendar's All Users default; Admin / Calendar Admin: all) and on the calendar it moves to; record appointments also need BO Edit and the field visible; Google / Office 365 events that are read-only can't be changed |
| Calendar management | As the web's Manage Calendars tab. Create a calendar or calendar group: Admin or Calendar Admin (you become its owner). Rename / recolor / `--all-users` / share / delete a calendar, and a group's `--all-users` / share / delete: its single owner, or an Admin / Calendar Admin (an Edit grant isn't enough). A group's name and calendars: Edit on the group. Moving a deleted calendar's events: Edit on the calendar they move to. Who a calendar or group is shared with shows only to those who can manage it |
| Email | `send-record-email` is sent as you, from your own mailbox |
| Feed, profile, notification settings | Always your own (the API scopes them to the signed-in user); use `--as-user` to work with someone else's |

**Pathway runs are exempt** from the business object create / edit / archive rules and the record viewer's visibility, as in the web app; their launch rules (`pathway-run start` / `available` / `execute-pathway`) still apply.

### Designer context (`context`)

Designers can write **context** on the account and on what they set up: business objects, fields (relationship fields too), field groups, relationships, milestones, workflow steps, tasks, stickers, folder stickers, file types, pathways and pathway steps. It is their own description of what the thing is for and how it's meant to be used. Anyone can read it.

- **The session context comes first.** On `use-account` and automatically before the first command of every session (the first after you sign in, too), the CLI gives you the session context: the account (id, name, time zone, country and its Account context, which describes the business), the pathways the user can start from the dashboard with their context, and the user's **process context** (what the designers wrote for the user's roles on the business objects they work with). Read it before acting. JSON output carries it as a `session_context` object (`{note, account, dashboard_pathways: [{id, name, context}], process_context: [{business_object_id, business_object, role, title, context}]}`) on the first JSON object of the session (for a top-level JSON array it is printed as text on stderr instead); text output prints it before the command's output. It is shown once per session: run **`session-context`** to reload it in full (for example after your context was compacted), or pass `--with-context`. Under `--as-user` it is not reloaded (you keep your own). The plugin's MCP server also puts the account in its instructions when the saved account is known at start.
- **Read an object's context before acting on it.** It comes back as `context` in get / list JSON (`get-business-object`, `get-field`, `list-fields`, `list-pathways`, `list-workflow-*`, …) and as a `Context:` line in `get-*` text output. Lists only show it in JSON.
- **Folder stickers:** `list-folders` gives each `folder_stickers[]` entry its `context`. A copy of a business object sticker with no context of its own also gets `bo_sticker_context`, which is the business object sticker's context that the copy shows. Never save `bo_sticker_context` into a folder. To change a folder sticker's own context: `update-folder --folder-id <id> --data '{"sticker_context":{"<sticker name or id>":"…"}}'` (`null` clears it, and a copy shows its business object sticker's context again). Folder, folder-sticker, report and report-column context, filters and creation are only in this CLI; the Designer CLI has none of them.
- **Pathway runs** show the pathway's, the step's and each field's context at the screen where it matters (see the `cli-pathways` command).
- **Pathways and tasks for the request.** The pathways the user can start from the dashboard come with the session context. When the request is about a particular record (any kind of record), open it: `get-record` gives `available_pathways` (its Pathways tab, the same as `pathway-run available --record-id <id>`) and `open_tasks` (the tasks already on it, each with its `context` and, in `pathway`, the pathway completing it runs). Read each one's `context` (what it is for) to decide whether one does what the user asked. A pathway or task is the business's own process: it fills the right fields, creates and links the right records, moves the record through its workflow and sends what should be sent. So when one fits, offer it before doing the same thing with raw edits (`create-record`, `update-record`, `run-step`); a task's pathway runs by completing the task (`pathway-run start --pathway-id <id> --record-id <id> --task-id <task id>`). Context already shown this session comes back left out (`context_elided`): it is tracked, not missing.
- **A record that isn't found.** When the user means one particular record and a search finds nothing that matches, try other spellings and forms (typos, sound-alikes, part of the name, without LLC/Inc, with and without punctuation, through the related customer or address) before asking or giving up. Ask only after those come up empty, saying what you searched for.
- **Records come with the context of what they show.** `get-record` adds `business_object {id, name, context}`, `field_context {<field>: context}` and `relationship_context {<relationship>: context}`. `list-records --folder-id` answers `{folder: {id, name, context}, business_object, field_context: [{field_id, name, context}], items}` (`folder.context` is the folder's own context: what it is for, who uses it; `items` is the record list as before; recent records give `business_objects[]` per BO). `run-report --json` keeps `{rows, meta}` and adds `report {id, name, context}` (the report's own context), `business_object`, `columns[] {name, column_type, field_id?, relationship_path?, context}` and `custom_fields[] {name, column_type, context}` (filter-only Custom Fields: not shown, but the filter compares them). A report column's `context` is its own when the report has one on it, otherwise its field's; calculated, count and Time Between columns only have their own. `list-folders`, `list-reports` and `get-report` carry each folder's / report's `context` (text output lists it under the table). `list-related-records` gives the relationship's, the related BO's and each column's context. A table column has no context of its own: it shows the context of the field (or relationship field, relationship or pathway) it is based on. A relationship field with no context of its own shows the related field's.
- **Context is given once per session.** The first time a piece of context appears in a session it is shown in full; later calls leave it out. JSON then drops the key and flags it (`context_elided: true`, `<key>_elided: true`, or `field_context_elided: [names]` for a map), with one `context_shown_earlier` summary on the object. Text prints `<label>: (shown earlier this session)` and one closing line. Pass **`--with-context`** to get it all again (the session context too), for example after your context was compacted, or when you are a subagent that didn't see the earlier calls. There are no exceptions: a second view of the same folder or report leaves out its folder / report, column and business object context, and a second run of the same pathway leaves out its pathway, step and field context. Only `get-account` and `session-context` (explicit requests) always show it. Context you just saved (`update-folder` / `update-report --context`) counts as shown. A missing `context` key never means "no context": a save copied from that output leaves the context unchanged.
- **Design metadata is fetched once per session.** Business objects, fields, field groups, relationships, milestones, workflow steps and tasks, pathways and their steps, folders, reports, stickers, calendars, users and roles are cached for the session (the same session as context), so repeating a list or lookup costs no request. Records and everything on them (tasks, files, messages, emails, history, notifications, appointments) and account settings are always live. A design change made through either CLI clears the cache, and a name or id that isn't in it is looked up again with fresh data. Changes someone else makes in the web app during the session show up after `clear-cache` (or `--no-cache` on one call).
- **Context is the designer's intent, not data.** Use it to understand what something is for, what to ask the user and what a sensible answer looks like. Never invent a value from it when the user hasn't decided.

## Commands

### Auth & Account Discovery
| Command | Description |
|---------|-------------|
| `authenticate` tool | Sign in via the browser. Use the tool, not the `auth` command: `auth` waits for the browser and times out inside `path_cli` |
| `auth_status` tool | Check the sign-in and list available accounts; names the saved default account with its Account context |
| `list-accounts` | List available accounts (no `--account` needed) |
| `use-account <id>` | Persist a default account ID to `~/.path-cli/.env` (replaces the need for `--account` on every command) and print the session context |
| `session-context` | Print the session context in full again: the account with its Account context, the dashboard pathways with their context and your process context (for example after your context was compacted) |
| `get-account [--account-id <id>]` | The active account: name, time zone, country and Account context (what the business does) |
| `logout` | Clear stored tokens and saved default account |

### Business Object Structure (read-only)
| Command | Description |
|---------|-------------|
| `list-business-objects` | List all BOs in the account |
| `get-business-object --bo-name <n>` | Get a single BO detail |
| `list-fields --bo-name <n>` | List fields on a BO |
| `list-field-groups --bo-name <n>` | List field groups on a BO |
| `list-relationships --bo-name <n>` | List relationships on a BO |
| `list-workflow-milestones --bo-name <n>` | List workflow milestones |
| `list-workflow-steps --bo-name <n>` | List workflow steps |
| `list-workflow-tasks --bo-name <n>` | List workflow tasks |

All BO commands accept `--bo-id <id>` or `--bo-name <name>`. These are read-only; changing the structure is Designer work.

### Folders & Records
| Command | Description |
|---------|-------------|
| `list-folders` | List all folders (filter: `--bo-name <n>`); each folder carries its `context`, and folder stickers carry theirs (copies of a BO sticker add `bo_sticker_context`) |
| `list-records --folder-id <id>` | List records in a folder. JSON: `{folder, business_object, field_context, items}` — the folder's own context, its BO's context and its columns' field context (once per session), records in `items` |
| `list-records` | List recent records (no folder). JSON: `{business_objects: [{id, name, context, field_context}], items}` |
| `get-record --record-id <id> [--raw]` | Get a single record. The record viewer's field names and display values on your clock; `--raw` / explicit `--json` give the stored record (ids, UTC). Adds `business_object`, `field_context` and `relationship_context` (only entries with context); `related_ids` gives each related record's id; `notes` gives each Notes field's count and newest 3 notes |
| `list-related-records --record-id <id>` | Every related list the record viewer shows you on that record: direct relationships (the records it links to — relationship cards, Contacts, People, Addresses) and inverse relationship tabs (records linking to it), each with `direction`, relationship id, name, related BO `{id, name, context}`, relationship `context` and a count |
| `list-related-records --record-id <id> --relationship-id <id> \| --relationship-name <name> [--direction direct\|inverse]` | That list's records with the record viewer's columns (inverse tab: its Table Columns, incl. lookups, nested tables, pathway buttons, milestones; direct: identifier, milestones and the linked fields; addresses: Street / City / State / Zip). JSON `{record, direction, relationship, business_object, columns[{kind, name, field_id?, relationship_id?, pathway_id?, context}], items[{id, identifier, values, linked_ids?}]}`. Open a listed record with `get-record --record-id <id>`. A relationship the record viewer doesn't show you is refused |
| `list-record-files --record-id <id> [--file-type <ids-or-names>]` | List every file on a record with its type names; `--file-type` keeps only files of those types (comma-separated ids and/or names) |
| `download-record-file --record-id <id> --file-id <id> [--out <path>] [--stdout]` | Download one record file (`--file-id` = `id` or `file_id` from list-record-files); saves to `./<file name>` by default, `--out` takes a file or directory, `--stdout` prints the bytes. File server = API host with `api`→`files` (override `PATH_FILE_SERVER_URL`) |
| `list-file-types --bo-id <id> \| --bo-name <name>` | Every file type on a BO (all pages fetched; id, type_name, system_type, active) |
| `list-record-tasks --record-id <id>` | List tasks on a record |
| `list-record-messages --record-id <id>` | List messages on a record |
| `list-record-notes --record-id <id> [--field <id\|name>]` | Every Notes field the record viewer shows you on the record, with all its notes newest first: note id, author, time (your clock), `mine` (only the author can edit/delete). JSON `{record, id, notes_fields[{field_id, field, can_add, count, notes[{id, author, created_by, created, created_at, updated_at?, mine, note}]}]}` |
| `list-record-emails --record-id <id>` | List emails on a record |

### Record History (read-only)
| Command | Description |
|---------|-------------|
| `get-history-by-record --record-id <id>` | Flat one-row-per-event table across every session of the record (see columns/flags below) |
| `get-event-metadata --event-id <id>` | Metadata + context for one history event (metadata only — field changes are not returned by that endpoint; use `--full`/`--json` on the list commands for diffs) |
| `get-history-session <activity-session-id>` | Every event one activity session recorded (e.g. everything an inbound request did), oldest first, with RECORD and EVENT columns; `--newest-first`, `--full`, `--metadata-keys`, `--max-width`. Needs the `GET /v1/tracking/history/sessions/:id` route on the server |

Designer, integration and user history are Designer work (PATH's Designer, or the PATH Designer plugin if installed).

**Columns:** `TIME` (`M/d/yy h:mma`, local) · `SESSION` (`#n`, numbered in output order) · `TYPE` (`card view`, `pathway: <name>`, `scheduled action: <desc>`, …) · `USER` (name, `system` for automated) · `RECORD` (user history only) · `ACTION` (label from the shared registry, e.g. `Saved Changes`, `Milestone Reached`) · `SUBJECT` (milestone/relationship/task/file/step name) · `CHANGES` · `METADATA` · `EVENT` (with `--ids`).

**Filters:** `--from`/`--to <iso>` (event time), `--action <action>`, `--category a,b` (comma list of `saved_changes`, `milestones`, `milestones_exited`, `steps`, `files`, `tasks`, `documents`, `notifications`, `emails_texts`, `messages`, `integrations`, `logistics`, `custom_code`).

**Fetching:** every session page is fetched by default (`--limit <n>` page size, max 100); `--page <n>` fetches one page only. View-only sessions (record opened, nothing changed) are hidden unless `--views`.

**Output:** newest session/event first (`--oldest-first` to flip). `CHANGES` = `Field: old → new; …` (dates `M/d/yyyy h:mma`, `(empty)` for blanks, empty→empty entries skipped). `METADATA` = `key=value; …` with plumbing ids and the SUBJECT's own key dropped, foreign record ids kept (`linked_record_id`, `unlinked_record_id`, `item_record_id`), `*_user_id` values resolved to names, blobs summarized (`recipients=3`, `payload=812 chars`, long text trimmed). Both cells cap at `--max-width <n>` (default 60), cutting between entries and ending `(+N more)` — nothing is dropped silently. `--full` prints every entry, one per line (rows grow taller). `--metadata-keys k1,k2` restricts the METADATA column. `--ids` adds the EVENT column for `get-event-metadata`. `--sessions` gives the legacy one-row-per-session table; `--json` gives the raw session-grouped response. Names come from the resolver cache (one users + one pathways request per account).

```
TIME          | SESSION | TYPE                             | USER     | ACTION           | SUBJECT        | CHANGES                                                   | METADATA
--------------|---------|----------------------------------|----------|------------------|----------------|-----------------------------------------------------------|-------------------------------------------------
9/1/26 5:35pm | #1      | pathway: Select As Winning Quote | SD Admin | Linked           | QI Sales Order |                                                           | linked_record_id=6a9750…; pathway_step_name=Link…
9/1/26 5:35pm | #1      | pathway: Select As Winning Quote | SD Admin | Milestone Exited | No Balance     |                                                           | pathway_step_name=Link QI Job
9/1/26 5:35pm | #1      | pathway: Select As Winning Quote | SD Admin | Saved Changes    |                | Identifier: 26-0074-: $0.00 → 26-0074-01: $0.00 (+3 more) | pathway_step_name=Assign Fields
[17 events across 2 sessions; view-only sessions hidden, --views to include]
```

### Users & Roles (read-only)
| Command | Description |
|---------|-------------|
| `list-users` | List users in the account |
| `list-roles` | List roles in the account |

### Pathways
| Command | Description |
|---------|-------------|
| `list-pathways` | List available pathways |
| `pathway-run start --pathway-id <id> [--record-id <id>] [--task-id <id>] [--answers '<json>' --strict]` | Run a pathway like the web app; stops at each screen that needs input (changes real data) |
| `pathway-run start --pathway-id <id> --copy-of <record id> [--copy-set "Field=value"]` | Run on a fresh copy of a record (test data) |
| `pathway-run test <scenario files or folders> [--keep]` | Repeatable tests: each makes its own records (`setup`), checks the outcome (fields, milestones, tasks, created records, launch denied) and archives its records; exit 1 on failure |
| `pathway-run save-test [--run <id>] --out <file> [--name …] [--copy-start]` | Save a finished run as a test: its answers, and what it did as the checks |
| `pathway-run cleanup [--run <id>]` | Archive a run's copy and the records it created |
| `pathway-run submit …` / `act …` / `show` / `cancel` | Answer the paused screen (only what it accepts), search on it, reprint it, or cancel |
| `pathway-run available [--record-id <id>] [--from record\|dashboard\|folder\|task] [--all]` | What the user can launch there, each with its context (what it is for), and with `--all` why the rest can't be |
| `pathway-run list` / `log [--api]` | Saved runs, a run's full trace |

### Tasks, Notifications & Calendars
| Command | Description |
|---------|-------------|
| `list-user-tasks` | Tasks assigned to the acting user (their dashboard list) |
| `list-notifications` | List notifications (`--unread` filter) |
| `list-feed [--unread --pinned --bo <id\|name> --record-id <id> --title <t> --content <t>]` | Your dashboard Feed, newest first |
| `feed-unread-counts [--by bo\|record]` | Unread Feed counts per BO (menu badges) or per record |
| `feed-records [--bo <id>]` | Records your Feed / popups mention |
| `pin-notification <id> [--unpin]` / `mark-notification-read <id> [--popup]` | Pin / unpin, mark read (your own items only) |
| `mark-all-notifications-read [--record-id <id>] [--mediums feed,popup]` | Mark all (or one record's) Feed items read |
| `list-popups [--unread]` | Your popup notifications |
| `get-profile` / `update-profile [--first-name --last-name --time-zone account\|local\|<IANA zone>]` | Your profile settings (name also written to your Person record) |
| `get-notification-settings [--resolved]` / `update-notification-settings [--set task.email=off …] [--phone <n>] [--data <json>]` | Your notification channels and text-message phone |
| `list-calendars` | List calendars |
| `get-calendar <id\|name>` | A calendar's color, owner, what All Users get (view / edit / none) and — for its owner or an Admin / Calendar Admin — who it is shared with |
| `list-calendar-groups` | Calendar groups you can see, with their calendars and owner |
| `get-calendar-group <id\|name>` | A group's calendars, owner, All Users level and (for managers) who it is shared with |
| `list-calendar-events` | List events of every origin (`--start-date` / `--end-date`: `YYYY-MM-DD` = that whole day in your time zone (end inclusive), `"YYYY-MM-DD HH:mm"`, today, tomorrow, +3d, or an ISO timestamp with Z/offset as-is; times shown on your clock, all-day events as account-calendar dates; `--json` keeps UTC ISO; `--calendar-ids`, `--sources standalone,record_appointment,provider`, `--record-id` [+ `--include-related`: appointments of linked and linking records, like the Appointments tab], `--search`); alias `list-appointments` |
| `list-appointment-fields [--bo-id\|--bo-name]` | Appointment fields events can be scheduled on |

### Web links
| Command | Description |
|---------|-------------|
| `web-link record <record-id> [--tab files\|messages\|email\|history\|pathways\|…]` | Link that opens the record in the web app (optionally on a tab); checks the record exists |
| `web-link folder <folder-id\|name> [--bo <id\|name>]` | Link to a folder in the web app; `--bo` picks between same-named folders |
| `web-link report <report-id\|name> [--bo <id\|name>]` | Link to a report in the web app |

Plain output is just the URL; `--json` adds type, id, name and business object. Links use the saved web app address (`PATH_WEB_BASE_URL`, default `https://pathwd.app`) and the current account.

### Tags & Reports
| Command | Description |
|---------|-------------|
| `list-tag-groups` | List tag groups |
| `list-reports` | List reports (only those the acting user can open); each carries its `context` |
| `run-report --report-id <id> [--per-page <n> | --all] [--user-filter '<json>']` | Run a report and print its rows (50 a page; `--all` for every row), then its root BO's context and each column's context (the field it shows; a column through relationships: the end field's, with `relationship_path`). `--json` keeps `{rows, meta}` and adds `report` (its own `{id, name, context}`), `business_object` and `columns` |

### Search
| Command | Description |
|---------|-------------|
| `search --query <text>` | Global search (`--bo-id <id>` / `--bo-name <name>` to filter; `--include-archived` to include archived records, left out by default) |
| `find-records --bo-name <n> --logic '<formula>'` | Advanced search: records matching a Logic Builder formula, nothing saved; only fields the user sees; `--catalog` lists what they can search on (with context); `--columns`, `--sort`, `--include-archived`, `--count`, `--all` |
| `find-records --bo-name <n> --logic '<formula>' --group-by '<fields>' [--sum\|--avg\|--min\|--max '<fields>'] [--sort '<column> desc'] [--top <n>]` | Grouped search: one row per group with its record count and totals (a grouped report run on the fly, groups only, nothing saved); `"<date> by month"` groups dates; only fields every record shows the user |

### Record Write Commands
| Command | Description |
|---------|-------------|
| `create-record --bo-name <n> --data <json>` | Create a record |
| `update-record --record-id <id> --data <json>` | Update a record |
| `link-record --record-id <id> --related-record-id <id> --relationship-id <id>` | Link two records |
| `unlink-record --record-id <id> --relationship-id <id>` | Clear a record's pointer on a relationship |
| `archive-record --record-id <id>` (or `--record-ids <id,id,...>`; `--bo-id <id>` skips the lookup) | Archive record(s) via the Archive Record step; already-archived ones are reported, not re-archived |
| `unarchive-record --record-id <id>` | Unarchive a record |
| `run-step --record-id <id> --bo-name <n> --step-id <id>` | Run a workflow step (triggers milestone evaluation) |
| `execute-pathway --pathway-id <id> --record-id <id> [--mode super-user]` | Run a pathway **headlessly** on a record, under the same launch rules as the record's Pathways tab: Yes/No questions answer No, steps that need a person are skipped; reports success even if a step failed, so verify the record |
| `send-message --record-id <id> --message <text> [--to <users>]` | Post a message on a record; `--to` @mentions and notifies users (ids, emails or full names), as the web's message box does |
| `add-record-note --record-id <id> [--field <id\|name>] --note <text> \| --note-file <path>` | Add a note to a Notes field, posted as you (the field's Add Note button; `--field` optional when the record has one Notes field). Needs BO Edit and the field visible. Not `update-record` |
| `update-record-note --record-id <id> --note-id <id> --note <text> \| --note-file <path>` | Replace the text of your own note (note ids: `list-record-notes`) |
| `delete-record-note --record-id <id> --note-id <id>` | Delete your own note (permanent) |
| `complete-task --record-id <id> --task-id <id>` | Mark a task complete |
| `cancel-task --record-id <id> --task-id <id>` | Cancel (skip) a task |
| `create-task --record-id <id> [--template <id\|title> \| --title <t>] [--assign-to …] [--due …] [--notes …]` | Create a task (New Task); `--list-templates` lists the templates offered |
| `edit-task --record-id <id> --task-id <id> [--title …] [--assign-to …] [--due …] [--notes …] …` | Edit an open task (creator/admin: everything; others: the assignee only, when allowed) |
| `assign-task --record-id <id> --task-id <id> --user-id <id>` | Assign a task to a user (legacy `--data '{"task_id","user_id"}'` still accepted) |
| `create-folder --bo-name <n> --folder-name <n> [--logic <formula>] [--map-relationship <name-or-id>] [--context <text> | --context-file <path>] [--dry-run]` | Create folder with optional permissions and its context (what it is for, who uses it); `--logic` sets `filter_condition` as a Logic Builder formula — see the `path-logic-formula-syntax` skill; `--map-relationship` enables Folder Map via a relationship to the account's Address BO **First** load the BO: `get-business-object` and `list-fields --json` for it (and `list-relationships`, plus `list-fields` of any related BO the filter reaches) — field names, types and context decide the right fields; skip what this session already loaded, re-run with `--with-context` if your context lost it. |
| `create-report --bo-name <n> --name <n> --columns '<specs>' [--column <spec>]… [--custom-field <spec>]… [--logic <formula>] [--type detail\|grouped] [--include-archived] [--context <text> \| --context-file <path>] [--column-context '<json>'] [--roles <n> --role-permissions view,edit,delete] [--users <n> --user-permissions …] [--data '<json>'] [--dry-run \| --preview [--groups-only] [--page/--per-page/--all]]` | `--preview` runs the report as built and prints its rows without saving it (no `--name` needed) — show the user before creating. Create a report the way the web report builder does; you become its owner (`--users` / `--roles` share it). Column specs: `Field`, `Relationship.Field`, `milestone:<n>`, `step:<n>`, `milestones`, `count`, `CALC(<expression>)`, `TIMEBETWEEN(<from>, <to>)`, then `\| name=… \| group[=month\|<range>] \| total\|average\|minimum\|maximum \| sort=asc\|desc \| context=…` and more (see `--help`). `--logic` can compare its own computed columns / Custom Fields (`COLUMN("…")`, `CUSTOMFIELD("…")`). Needs Create Reports on the BO. Prints the id and web link. Workflow: the `cli-reports-skill` command **First** load the BO: `get-business-object` and `list-fields --json` for it (and `list-relationships`, plus `list-fields` of any related BO the filter or a column reaches) — field names, types and context decide the right fields; skip what this session already loaded, re-run with `--with-context` if your context lost it. |
| `update-folder --folder-id <id> \| --folder-name <n> --bo-name <n> [--context <text> \| --context-file <path>] [--data '<json>'] [--dry-run]` | Change a folder's context or settings, saved the way the folder form does (everything else as stored, map settings kept). `--data` keys: `folder_name`, `include_archived`, `columns`, `sorts`, `default_sort_field`, `default_sort_direction`, `map_enabled`, `map_relationship_id`, `context` (`null` or `""` clears it), `sticker_context` (`{"<sticker id or name>": "…" \| null}`: each named sticker's own context; custom folders only). Criteria: `set-filter folder`. Needs Edit on the folder |
| `update-report --report-id <id> \| --report-name <n> [--context <text> \| --context-file <path>] [--data '<json>'] [--dry-run]` | Change a report's context or settings, saved the way the report form does (columns, filter, scheduling as stored). `--data` keys: `name`, `include_archived`, `context`, `column_context` (`{"<column id, column_key or name>": "text" \| null}`: each named column's or Custom Field's own context; null clears it, so the column shows its field's again). Filter: `set-filter report`; Custom Fields: `create-custom-field`. Needs Edit on the report |
| `create-calendar-event --title --start --calendar [--end --all-day --multi-day --color --location --description --record <id> --field <id\|name>]` | Create a calendar event (times in the user's zone, all-day days on the account calendar, calendar by id or name); `--record`/`--field` makes it that record's appointment; `--data <json>` still accepted (flags override); alias `create-appointment` |
| `get-calendar-event <id>` | One calendar event |
| `update-calendar-event <id> [same flags] [--record <id> --field <id\|name> \| --unlink] [--data <json>]` | Update an event (record appointment field re-projected); link it to a record's appointment field or `--unlink` it |
| `set-appointment <record-id> --field <id\|name> [--start --end --calendar --title --all-day --multi-day …]` | Schedule or change a record's appointment field (creates/updates the event that owns it; new title from the Event Description template) |
| `clear-appointment <record-id> --field <id\|name>` | Clear a record's appointment field (deletes its event) |
| `delete-calendar-event <id>` | Delete an event (clears a record appointment field) |
| `create-calendar --name <n> [--color <#hex>]` | Create a Standard calendar (the web's Add Calendar; default color #1A5E20); you become its owner and everyone can view it. Admin / Calendar Admin only |
| `update-calendar <id\|name> [--name <n>] [--color <#hex>] [--all-users view\|edit\|none]` | Rename / recolor a calendar, or set what every member gets without another grant (`none`: only those it's shared with — Admins / Calendar Admins still see it). Owner or Admin / Calendar Admin |
| `share-calendar <id\|name> [--users <ids,emails,names>] [--roles <ids,names>] --access view\|edit\|owner\|remove` | The calendar's Permissions tab: give users / roles View or Edit (edit implies view), make one user the owner (single owner; the previous owner keeps view/edit), or `remove` their row. Role "All Users" sets the All Users level. Owner or Admin / Calendar Admin |
| `delete-calendar <id\|name> [--move-events-to <calendar>] [--external-events delete\|leave] [--confirm <code>]` | Delete a calendar and its events (record appointments cleared), or first move the events to a calendar you can edit; a calendar merged with Google / Office 365 is disconnected (`--external-events`: the events PATH created there, default leave). **Asks for confirmation:** the first run deletes nothing and prints what it would delete plus a code — show it to the user and only after an explicit yes re-run with `--confirm <code>`. Owner or Admin / Calendar Admin |
| `create-calendar-group --name <n> --calendars <ids,names>` | Create a calendar group (the web's Add Group); you become its owner. Admin / Calendar Admin only |
| `update-calendar-group <id\|name> [--name <n>] [--calendars <list> \| --add-calendars <list> --remove-calendars <list>] [--all-users view\|edit\|none]` | Rename a group or change its calendars (Edit on the group); `--all-users`: owner or Admin / Calendar Admin |
| `share-calendar-group <id\|name> [--users …] [--roles …] --access view\|edit\|owner\|remove` | Share a group (edit = may rename it / change its calendars); never changes access to the calendars in it. Owner or Admin / Calendar Admin |
| `delete-calendar-group <id\|name> [--confirm <code>]` | Delete a group (its calendars and events stay). Asks for confirmation like `delete-calendar`. Owner or Admin / Calendar Admin |

### Filters (Logic Builder formulas)
| Command | Description |
|---------|-------------|
| `parse-filter --bo-name <n> --text '<formula>' [--report] [--allow-blank]` | Validate a Logic Builder formula (e.g. `[Status] = "Won" AND INMILESTONE("Lead")`) without saving; `--json` prints the GroupFilter — see the `path-logic-formula-syntax` skill |
| `get-filter <target> <id> [--bo <id>] [--pathway <id>] [--sticker <id>]` | Print a stored filter as formula text. Targets: `milestone`, `task-complete`, `task-cancel`, `task-filter` (need `--bo`), `fork`, `table-filter` (need `--pathway`), `pathway`, `sticker`, `folder`, `folder-sticker` (needs `--sticker`), `report`, `field-visibility`, `field-group-visibility`, `relationship-source-visibility`, `relationship-target-visibility`, `target-field-visibility`, `setting` (a logic account setting's logic), `bo-show-in-menu` (a BO's Show in Menu Logic override; id = BO id). Folder / report: ticked user filters follow as `user filter "Label": <rule>` |
| `set-filter <target> <id> --text '<formula>' [--user-filter '<row>'] [--untick-user-filter '<row>']` | Parse, validate and save a filter, keeping the rest of the object; `--clear` removes it, `--dry-run` validates without saving. Writes **folder**, **folder-sticker** and **report** targets only (with Edit on them); every other target is refused as Designer work. Folder / report: `--user-filter '["Label": ]<row>'` (repeatable) ticks a rule as a user filter, `--untick-user-filter '<row>'` unticks it; `<row>` = `[Field]` / `[Relationship.Field]` when one rule compares it, or the whole rule; works without `--text` (stored rules kept). See `/path-logic-formula-syntax` → User filters |
| `create-custom-field <report-id>` / `update-custom-field <report-id> <field>` / `delete-custom-field <report-id> <field>` | A report's filter-only Custom Fields (calculation `--expression`, or Time Between `--from/--to`: `today`, `field:<date field>`, `milestone:<name>`, `step:<name>`, each also on a linked record through one-to-one relationships, e.g. `milestone:Order Related PO.Received`); create / update take `--context` / `--context-file` (what it measures) |

### Escape Hatch
| Command | Description |
|---------|-------------|
| `api <method> <path>` | Raw HTTP call (`--data <json>` for body) |

## Examples

```
# Explore the data model
--account 1234 list-business-objects
--account 1234 list-fields --bo-name Equipment

# Browse records
--account 1234 list-folders --bo-name Equipment
--account 1234 list-records --folder-id 567
--account 1234 get-record --record-id 12345 --json

# Search
--account 1234 search --query "test"

# Create and operate on records
--account 1234 create-record --bo-name Equipment --data '{"fields":{}}'
--account 1234 execute-pathway --pathway-id 89 --record-id 12345
--account 1234 send-message --record-id 12345 --message "Updated via CLI"

# Raw API for anything not covered
--account 1234 api get /business-objects
--account 1234 api post /records --data '{"key":"value"}'
