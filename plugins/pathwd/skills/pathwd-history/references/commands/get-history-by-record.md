# get-history-by-record

Runs with: path_record_activity(command: "get-history-by-record", args: "<flags>") (read-only).

```
Usage: get-history-by-record [options]

Change history for a record as a flat event table (read-only). All sessions are
fetched unless --page is given.

Options:
  --record-id <id>        Record ID
  --from <date>           Only events at/after this time (ISO 8601)
  --to <date>             Only events at/before this time (ISO 8601)
  --action <action>       Filter by event action (e.g. updated)
  --category <list>       Filter by event category; comma-separated for several
                          (e.g. saved_changes,milestones)
  --page <n>              Fetch only this single page of sessions (disables
                          auto-pagination)
  --limit <n>             Sessions per page (max 100) (default: "100")
  --sessions              Session-grouped output (one row per session) instead
                          of the flat event table
  --metadata-keys <list>  Comma-separated metadata keys to show in the METADATA
                          column (default: all but plumbing)
  --views                 Include view-only sessions (record opened, nothing
                          changed); hidden by default like the History tab
  --oldest-first          Chronological order (default: newest session and
                          event first, like the History tab)
  --full                  Every field change and metadata entry in full, one
                          per line (rows grow taller instead of wider)
  --ids                   Add an EVENT column with each event id (for
                          get-event-metadata)
  --max-width <n>         Max characters for the CHANGES and METADATA cells;
                          cut between entries with "(+N more)" (0 = unlimited)
                          (default: "60")
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --from 2026-09-28`

## Notes

A record's change history, as its History tab shows it: one row per event (fields saved, links, milestones reached and exited, pathway steps, tasks, files, notes, emails, messages), grouped into sessions, newest first. Read-only. How to pick filters and turn the table into an answer: the [`path-record-history` guide](../record-history.md).

**Examples**

```
get-history-by-record --record-id 6adba16ca29a6293f946ca4f --from 2026-10-26
get-history-by-record --record-id 6adba16ca29a6293f946ca4f --category milestones,milestones_exited
get-history-by-record --record-id 6adba16ca29a6293f946ca4f --category saved_changes --from 2026-10-01 --to 2026-10-31T23:59:59-05:00 --full
get-history-by-record --record-id 6adba16ca29a6293f946ca4f --oldest-first --ids
get-history-by-record --record-id 6adba16ca29a6293f946ca4f --sessions
```

- Every page of sessions is fetched unless `--page` is given. `--from` / `--to` take ISO dates; a bare date is midnight UTC, so add the user's offset when the hour matters.
- `--category` (comma-separated) and `--action` (one internal action name such as `updated`, `milestone_reached`, `note_added`) narrow the events; the lists are in the skill.
- View-only sessions (the record opened, nothing changed) are left out of the table unless `--views`.

**Output**

An event table, newest first, then the sessions it numbers:

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

Lines to read:

- `time`: the user's clock. `session`: rows with the same `#n` happened in one sitting.
- `action`, `subject` (the milestone, step, task, file, relationship or email subject), `changes` (`Field: old → new`, `(empty)` for blank), `metadata` (`key=value`; `custom_code_method=<name>` first when custom code made the change).
- `Sessions:` one line per `#n`: what the session was (`card view`, `pathway: <name>`, `scheduled action`, `inbound integration`, `email received: …`), who (`system` = automation), the agent and client when one drove it (`Claude Code · PATH CLI`), and the session id `get-history-session` takes.
- A cut cell ends in `(+N more)`; `--full` (one entry per line) or `--max-width 0` shows all of it. `--ids` adds an `event` column with the ids `get-event-metadata` takes.

`--fields` picks columns by header (`time, session, type, user, source, action, subject, changes, metadata, event`); asking for `type`, `user` or `source` puts them back in the table (on each session's first row) instead of the `Sessions:` list. `--quiet` prints the event ids. `--sessions` prints one row per session instead: `session` (id), `started`, `type`, `user`, `source` (when any), `record`, `events`.

A record's history can include sessions that started on a linked record; sessions on records the user can't view are left out, with a note on stderr.

**Common errors**

- `<user> can't read the history of record … — record … isn't visible to them, as in the web app.` (or no View permission on its business object).
- No rows: nothing matched; widen the dates or drop `--category` / `--action`.

**Related:** `get-history-session`, `get-event-metadata`, `get-record`.

Related commands: [`list-record-files`](../../../pathwd-guide/references/commands/list-record-files.md), [`list-record-notes`](../../../pathwd-guide/references/commands/list-record-notes.md), [`list-record-messages`](../../../pathwd-guide/references/commands/list-record-messages.md), [`list-record-emails`](../../../pathwd-guide/references/commands/list-record-emails.md), [`list-record-tasks`](../../../pathwd-guide/references/commands/list-record-tasks.md), [`get-history-session`](get-history-session.md), [`get-event-metadata`](get-event-metadata.md).
