# get-history-session

Runs with: path_record_activity(command: "get-history-session", args: "<flags>") (read-only).

```
Usage: get-history-session [options] <activity-session-id>

Every event one activity session recorded — e.g. everything an inbound
integration request did: the record it created, fields set, pathway steps,
outbound calls — as the flat event table, oldest first (read-only)

Arguments:
  activity-session-id     Activity session id (the session column of
                          get-history-by-record --sessions; the session line of
                          an integration delivery)

Options:
  --newest-first          Newest event first (default: in the order they
                          happened)
  --full                  Every field change and metadata entry in full, one
                          per line
  --metadata-keys <list>  Comma-separated metadata keys to show in the METADATA
                          column (default: all but plumbing)
  --max-width <n>         Max characters for the CHANGES and METADATA cells (0
                          = unlimited) (default: "60")
```

## Notes

Every event one activity session recorded, across every record it touched, oldest first, as the History tab's Session view shows it. A session is one sitting: a visit to a record's card, one pathway run, one inbound integration request (the record it created, fields set, pathway steps, outbound calls). Read-only.

**Examples**

```
get-history-session 6adf681563a500a9a7a44bef
get-history-session 6adf681563a500a9a7a44bef --full --newest-first
```

- The session id is the last part of a `Sessions:` line under `get-history-by-record` (or the `session` column of `get-history-by-record --sessions`, or the session line of an integration delivery). The `#1`, `#2` numbers in the history table aren't session ids.
- It takes the id as an argument, not a flag.

**Output**

```
Session 6adf681563a500a9a7a44bef: inbound integration: OMAPI: Process Import Sets
user: PATHwd System (587) · system · 10/7/26 12:03pm – 10/7/26 12:03pm

time            | record                   | action              | subject                    | changes                                     | metadata                                   | event
----------------|--------------------------|---------------------|----------------------------|---------------------------------------------|--------------------------------------------|-------------------------------------
10/7/26 12:03pm | 6ac67b75dc615d8c41d5f0be | Created             |                            | Identifier: (empty) → INT10/7/26 (+21 more) |                                            | 97cd616b-dc86-4526-b6e6-a1dc55cf7ccc
10/7/26 12:03pm | 6ac67b75dc615d8c41d5f0be | Inbound Integration | OMAPI: Process Import Sets |                                             | integration_id=2444; method=POST (+9 more) | cb75f07d-cb16-4f2c-86d8-24d7612ccad9
[2 events]
```

Lines to read: the header names what the session was, who drove it (with a `Claude Code · PATH CLI` style source when an agent did), the platform, its start and end on the user's clock, and its record. Each row: `record` (the record the event is on), `action`, `subject`, `changes`, `metadata` (cut cells end in `(+N more)`; `--full` shows every entry), and the `event` id `get-event-metadata` takes. `--quiet`: the event ids. Reading the rows: the [`path-record-history` guide](../record-history.md).

**Common errors**

- `Session X not found`: a wrong id (or the `#n` number from the table).
- `<user> can't read history session … — record … isn't visible to them, as in the web app.`: Admins, Designers and User Admins can read any session; anyone else only sessions whose records they can open.
- `… — the session is on no record, and none of their roles has the Designer, User Admin (or Admin) permission, as in the web app.`

**Related:** `get-history-by-record`, `get-event-metadata`.

Related commands: [`list-record-files`](../../../pathwd-guide/references/commands/list-record-files.md), [`list-record-notes`](../../../pathwd-guide/references/commands/list-record-notes.md), [`list-record-messages`](../../../pathwd-guide/references/commands/list-record-messages.md), [`list-record-emails`](../../../pathwd-guide/references/commands/list-record-emails.md), [`list-record-tasks`](../../../pathwd-guide/references/commands/list-record-tasks.md), [`get-history-by-record`](get-history-by-record.md), [`get-event-metadata`](get-event-metadata.md).
