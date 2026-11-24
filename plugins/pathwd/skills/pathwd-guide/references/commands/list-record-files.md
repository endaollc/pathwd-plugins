# list-record-files

Runs with: path_record_activity(command: "list-record-files", args: "<flags>") (read-only).

```
Usage: list-record-files [options]

List every file attached to a record (optionally only the given file types),
with each file's type names

Options:
  --record-id <id>            Record ID
  --file-type <ids-or-names>  Only files carrying one of these file types
                              (comma-separated ids and/or names, names
                              case-insensitive)
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084`

## Notes

Every file on a record's Files tab, with each file's type names. That includes files on linked records when the relationship is set to include their files, so a Job's list often shows its Quotes' files; the `on` column says which record holds each of those. Archived files are listed too (`arch` is `yes`); the web's Files list hides them until "show archived" is on.

**Examples**

```
list-record-files --record-id 6aad6bfbd7f23b3a293e4aa7
list-record-files --record-id 6aad6bfbd7f23b3a293e4aa7 --file-type "Unsigned Full Quote,Full Quote Terms"
--fields id,name,type,arch list-record-files --record-id 6aad6bfbd7f23b3a293e4aa7
```

- No paging: every file comes back in one list (newest last).
- `--file-type` takes ids and/or names, comma-separated, names case-insensitive; a file matches when it carries any of them. Names cover the types of the record's business object and of the linked records whose files are listed (a name both use matches both). `list-file-types --bo-name <business object> --include-hidden` lists a business object's types.

**Output**

A table, one row per file (newest last):

```
id                       | name                          | type                 | by       | at               | arch | comment | on
-------------------------|-------------------------------|----------------------|----------|------------------|------|---------|------------------------------------------------
6ad11fb03628df72ac3a6b94 | Kessler Quote Rev 1.pdf       | Email Attachment     | JC Admin | 2026-10-15 13:47
6ad131223628df72ac3b2805 | Terms Sheet 26-1834-2.pdf     | Final Contract Terms | AB Admin | 2026-10-15 15:01 |      |         | Final Contract: 26-1834-02 (6ad13087e59d243cf080ff10)
6ad1312e3628df72ac3b281b | Old drawing.pdf               | Drawings/Plans       | AB Admin | 2026-10-15 15:01 | yes  | v1
[3 files]
```

Columns to read:

- `id`: the record file id (24 hex). `download-record-file`, `archive-record-file`, `unarchive-record-file`, `update-record-file`, `delete-record-file` and `send-record-email --record-file` take it.
- `name`, `type` (the file's types by name, comma-separated; a linked record's file shows its own business object's type names), `by` (who uploaded it), `at` (when, on the user's clock), `arch` (`yes`: archived), `comment`.
- `on`: blank for the record's own files; for a linked record's file, that record (`identifier (record id)`). The file commands take that record id as `--record-id`.

A type can be exclusive: only one file on the record holds it at a time (a new file of that type takes it off the older one). `--quiet` prints the ids only. `[0 files]` means the record has no files the user can see.

**Common errors**

- `Unknown file type "X". Known types: …`: the name isn't one of the listed files' business objects' file types; pick one from the list.
- `<user> can't list the files of record … — they don't have View permission on <business object>, as in the web app.`

**Related:** `download-record-file`, `upload-record-file`, `update-record-file`, `archive-record-file`, `unarchive-record-file`, `delete-record-file`, `list-file-types`, `send-record-email --record-file`.

Related commands: [`list-record-notes`](list-record-notes.md), [`list-record-messages`](list-record-messages.md), [`list-record-emails`](list-record-emails.md), [`list-record-tasks`](list-record-tasks.md), [`get-history-by-record`](../../../pathwd-history/references/commands/get-history-by-record.md), [`get-history-session`](../../../pathwd-history/references/commands/get-history-session.md), [`get-event-metadata`](../../../pathwd-history/references/commands/get-event-metadata.md).
