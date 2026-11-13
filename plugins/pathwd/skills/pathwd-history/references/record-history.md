# path-record-history

> **Commands:** every command in this skill is a PATH CLI command line: the command name and its flags. On PATHwd each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../../pathwd-guide/references/commands.md) gives any command's syntax and the tool that runs it.

Answer "what happened" questions from PATH's change history: the same events the History tab on a record shows. History is **read-only**. Nothing in this skill changes data, and nothing can edit or remove a history entry.

Commands below rely on the default account saved by the [`path-session` guide](../../pathwd-guide/references/session.md). Add `--account <ID>` only to target a different account for one call.

## What a history list looks like

`get-history-by-record` prints an event table, newest first, then the sessions it numbers:

```
time             | session | action            | subject        | changes                        | metadata
-----------------|---------|-------------------|----------------|--------------------------------|-----------------------------------------
11/5/26 9:30am   | #1      | File Uploaded     | site-photo.jpg |                                | mime_type=image/jpeg; file_size=5612
10/29/26 12:33pm | #2      | Saved Changes     |                | Status: Quoted → Won (+1 more)
10/29/26 12:33pm | #2      | Milestone Reached | Won            |                                | pathway_step_name=Win Job
Sessions:
#1 card view · Pat Lee · Claude Code · PATH CLI · 6aeca0f96e6a942eaa8897db
#2 pathway: Win Job · Pat Lee · 6ae3832bfef6b5c3bd1bee5d
[3 events across 2 sessions; view-only sessions hidden, --views to include]
```

User and pathway names are filled in, times are on the user's clock, and long cells are cut (see **Reading the table**).

## What you need from the user

1. **Whose history**: a record ("Job 12345", "the Smith invoice").
2. **The question**: what they want to know (a field change, a milestone, a pathway run, everything).

Optional, do NOT ask; infer from the question or leave out:
- **Time window**: "this week", "yesterday", "since Monday". No window means the record's whole history, which is fine for one record.
- **Kind of event**: infer it from the question (see "Narrow with filters").

## Workflow

### 1. Resolve the record

**A record.** If the user gave a 24-character internal ID, use it. Otherwise search:

```
search --query "12345" --bo-name Job
```

Leave out `--bo-name` if they didn't say what kind of record it is. One hit: use its `id`. Several: list them and ask which one. None: say so and ask them to check the number or name.

**A person's history** (everything one user did) is Designer work: an Admin or User Admin sees it in PATH's Designer (or with the PATH Designer plugin's `get-history-by-user`, if installed). Here, answer what changed on records.

### 2. Turn the time window into dates

`--from` and `--to` take ISO 8601 dates or date-times: `2026-10-05` or `2026-10-05T14:00:00-05:00`. Work out real dates from today's date ("this week" means from Monday; "yesterday" means from yesterday 00:00 to today 00:00). A bare date is read as midnight UTC, so when the exact hour matters, include the user's UTC offset.

### 3. Run the narrowest query that answers the question

Record history:

```
get-history-by-record --record-id <RECORD_ID> [filters]
```

That command fetches every matching page on its own; you don't need to page through results.

### 4. Answer in plain language

Read the table and answer the question in a sentence or a short list. Don't paste the table unless the user asks for it.

## Reading the table

One row per event, newest first:

| Column | Meaning |
|---|---|
| `time` | When the event happened, in the user's local time (`10/5/26 2:14pm`) |
| `session` | `#1`, `#2`, ... Rows with the same number happened in one sitting (one card-view visit or one pathway run) |
| `action` | What happened: `Saved Changes`, `Created`, `Linked`, `Unlinked`, `Archived`, `Milestone Reached`, `Milestone Exited`, `Workflow Step Run`, `Task Completed`, `File Uploaded`, `Email Sent`, `Message Sent`, `Note Added`, `Document Generated`, ... |
| `subject` | What the event was about: the milestone, step, task, file, relationship or email subject |
| `changes` | Field changes as `Field: old → new`. `(empty)` means blank |
| `metadata` | Extra detail as `key=value`, e.g. `pathway_step_name=Assign Fields`. `custom_code_method=<name>` comes first when custom code made the change |

Under the table, `Sessions:` has one line per `#n`: `#<n> <type> · <user> [· <source>] · <session id>`.

| Part | Meaning |
|---|---|
| type | Where it happened: `card view` (the record's card), `pathway: <name>`, `scheduled action`, `inbound integration`, `email received: …`, ... |
| user | Who did it. `system` (or the account's system user, e.g. `PATHwd System`) means automation, not a person |
| source | Present only when the session has one. An agent and where the work came from, e.g. `Claude Code · PATH CLI` (Claude, through the PATH CLI tools, signed in as the session's user), `Claude Cowork · PATH CLI`, or `PATH CLI` alone (the person typed CLI commands). Absent: the web app, or a session from before sources were recorded |
| session id | What `get-history-session` takes |

The footer reports the counts, e.g. `[17 events across 2 sessions; view-only sessions hidden, --views to include]`.

How to turn rows into an answer:
- **Who and how**: the session's user and type (its `Sessions:` line). "Sarah Lee moved it to Won on 10/2 at 3:40pm by running the Win Job pathway."
- **Automation**: when the session's user is `system` (or the system user), say the change was made automatically (a scheduled action, integration or workflow), not by a person.
- **An agent**: when a session's source names an agent, say so with the person it acted for: "Claude Code, signed in as Sarah Lee, moved it to Won". When `metadata` has `custom_code_method`, name the method: "the ProcessInvoice custom code set the total".
- **One sitting**: rows sharing a `session` number belong together. A pathway run often saves fields, reaches a milestone and links a record in one session; describe it as one action.
- **Field changes**: quote old and new values: "Status went from Quoted to Won."
- **Nothing found**: say there were no matching events in that window, and offer to widen it or drop a filter.

## Narrow with filters instead of dumping everything

A busy record can have hundreds of events. Pick filters from the question:

| User asks | Add |
|---|---|
| "this week", "yesterday", "since Monday" | `--from <date>` and/or `--to <date>` |
| "who moved it to Won?", "when did it leave Lead?" | `--category milestones` (reached) or `--category milestones_exited` |
| "when was the status changed?", "who edited the price?" | `--category saved_changes`, then look for the field in `changes` |
| "which pathways were run?", "what steps ran?" | `--category steps` |
| "who uploaded that file?" | `--category files` |
| "who completed the task?" | `--category tasks` |
| "what emails or texts went out?" | `--category emails_texts` |
| "was a document generated?" | `--category documents` |

Categories: `saved_changes`, `milestones`, `milestones_exited`, `steps`, `files`, `tasks`, `documents`, `notifications`, `emails_texts`, `messages`, `integrations`, `logistics`, `custom_code`. Combine with commas: `--category milestones,saved_changes`. `saved_changes` covers field edits, creation, links, unlinks, archive/unarchive and notes.

`--action <name>` filters to one exact action. It takes the internal name, not the label: `updated` (Saved Changes), `created`, `linked`, `unlinked`, `archived`, `milestone_reached`, `milestone_exited`, `step` (Workflow Step Run), `task_completed`, `file_uploaded`, `email_sent`, `note_added`.

There is no filter for one field. For "when was the status changed?", filter to `--category saved_changes` and read `changes`.

There is no filter for who made a change either. For "what did Claude change?", read the `Sessions:` lines: an agent's sessions name the agent.

## Display options

| Flag | Use it when |
|---|---|
| `--full` | A `changes` or `metadata` cell ends in `(+N more)` and you need the rest. Prints every change, one per line |
| `--max-width <n>` | Widen the cells instead (default 60 characters; `0` = no limit) |
| `--oldest-first` | The user wants a timeline in order ("walk me through what happened") |
| `--views` | The user asks who **opened** or looked at the record. View-only visits are hidden by default. Don't combine with `--from`/`--to`/`--category`/`--action`: any filter drops sessions with no matching events, which removes the views. Read the dates from `time` instead |
| `--metadata-keys <k1,k2>` | Show only some metadata keys, e.g. `--metadata-keys pathway_step_name` |
| `--ids` | Add an `event` column with event IDs, for `get-event-metadata` |
| `--sessions` | One row per session instead: `session` (id), `started`, `type`, `user`, `source` (when any), `record`, `events` |
| `--page <n>` / `--limit <n>` | Fetch one page of sessions only (up to 100 per page). Rarely needed |

A cut cell always ends in `(+N more)`, so nothing is dropped silently. To save tokens, choose columns with the global `--fields` option (before the command name), by header: `--fields time,user,type,action,subject,changes get-history-by-record --record-id <ID>`. Asking for `type`, `user` or `source` puts them back in the table (on each session's first row) instead of the `Sessions:` list. `--quiet` prints the event ids alone.

## Sessions, and history in PATH's Designer

- `get-history-session <session id>` shows everything one activity session did (for example everything an inbound request did): the record it created, fields set, pathway steps, outbound calls. Take the session id from a history list's `Sessions:` lines (or `--sessions`), or from an integration delivery. It needs Admin, Designer or User Admin, else the session's record must open as the acting user.
- **Designer history** (setup changes across the account), **integration history** (deliveries, replays) and **user history** (`get-history-by-user`) are Designer work: an Admin or Designer sees them in PATH's Designer (user history: Admin or User Admin), or with the PATH Designer plugin if it's installed.

## Who can see what

Every command runs with web-app access for the acting user (`--as-user`, else the signed-in user; `--as-user` is for account Admins only). A record's history is only for a record they can open, and sessions on other records they can't view are left out (a stderr line says how many). Times are on that user's clock.

## One event in detail

To see everything recorded about a single event (every metadata entry, the session it belongs to, the record and user IDs), run the list with `--ids`, take the event ID from the `event` column, then:

```
get-event-metadata --event-id <EVENT_ID>
```

It prints `event`, `action`, `category`, `at`, `user`, `entity` (the record id) and `session` lines, then every `metadata:` entry, one per line. Field changes are not included; use `--full` on the history list for those.

## Examples

"Who moved Job 12345 to Won?"
```
search --query "12345" --bo-name Job
get-history-by-record --record-id <RECORD_ID> --category milestones
```
Find the `Milestone Reached` row whose `subject` is `Won`; report its `time`, and the user and type from its session's `Sessions:` line.

"What changed on this invoice this week?"
```
get-history-by-record --record-id <RECORD_ID> --from 2026-10-05 --category saved_changes --full
```

## Common errors

| Problem | What to do |
|---|---|
| Record not found / 404 | The record ID is wrong or belongs to another account. Re-run `search` and check the active account |
| No events, but the user expects some | Widen the dates, drop `--category` / `--action`, and check that the record is the right one |
| Names show as numbers in a `Sessions:` line | A user added in the last few minutes. Re-run with `--no-cache` before the command name |
| Authentication error | Run the [`path-session` guide](../../pathwd-guide/references/session.md) to sign in again |

## Important rules

- History is read-only. This skill never changes records, and history entries can't be edited or deleted.
- Never guess record IDs. Resolve them with `search`.
- Filter to the question (`--from`/`--to`, `--category`) before reading a whole history.
- Answer in plain language. Refer to records by their identifier ("Job 12345"), not internal IDs, and to people by name.
- `system` means an automatic change. Don't attribute it to a person.
