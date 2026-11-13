# list-record-messages

Runs with: path_record_activity(command: "list-record-messages", args: "<flags>") (read-only).

```
Usage: list-record-messages [options]

List the messages on a record, newest first (deleted messages left out)

Options:
  --record-id <id>  Record ID
  --page <n>        Fetch only this single page (default: all messages)
  --per-page <n>    Items per page (single-page mode) (default: "15")
  --all             Fetch all messages (this is now the default)
```

## Notes

The messages on a record's Messages, newest first: the conversation PATH users have on the record, with @mentions. Not the record's Notes fields (`list-record-notes`) or its Emails (`list-record-emails`).

**Examples**

```
list-record-messages --record-id 6aa15f317c0cf38a06d4160b
list-record-messages --record-id 6aa15f317c0cf38a06d4160b --page 1 --per-page 100
list-record-messages --record-id 6aa15f317c0cf38a06d4160b --page 2 --per-page 25
```

- Without `--page`, every message on the record comes back, however many there are. `--page` / `--per-page` fetch one page of the newest-first list instead.
- Deleted messages are left out, as in the web app's Messages tab.

**Output**

A table, newest first:

```
id                       | at               | by       | to      | message
-------------------------|------------------|----------|---------|-----------------------------------------
6af50e284e2efa22bf895258 | 2026-11-11 18:54 | JC Admin | Pat Lee | @Pat Lee the truck is booked for Friday
6af50e284e2efa22bf895246 | 2026-11-11 16:02 | Pat Lee  |         | Customer asked to move the install
[3 of 19 messages — --page 2 for more]
```

Columns to read:

- `id`: the message id `update-message` / `delete-message` take.
- `at`: when it was posted, on the user's clock. `by`: the author (only the author can edit or delete it). `to`: the mentioned users (each was notified).
- `message`: the text (mentions read `@Name`), cut past 120 characters with `…`.

`--quiet`: ids only. `--fields at,message` picks columns.

**Common errors**

- `<user> can't read the messages of record … — they don't have View permission on <business object>, as in the web app.`
- `[0 messages]`: no messages on the record.

**Related:** `send-message`, `update-message`, `delete-message`, `list-record-notes`, `list-record-emails`.

Related commands: [`list-record-files`](list-record-files.md), [`download-record-file`](download-record-file.md), [`list-record-notes`](list-record-notes.md), [`list-record-emails`](list-record-emails.md), [`list-record-tasks`](list-record-tasks.md), [`get-history-by-record`](../../../pathwd-history/references/commands/get-history-by-record.md), [`get-history-session`](../../../pathwd-history/references/commands/get-history-session.md), [`get-event-metadata`](../../../pathwd-history/references/commands/get-event-metadata.md).
