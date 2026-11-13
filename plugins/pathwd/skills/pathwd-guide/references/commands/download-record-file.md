# download-record-file

Runs with: path_record_activity(command: "download-record-file", args: "<flags>") (read-only).

```
Usage: download-record-file [options]

Download one file attached to a record (to a workspace path, or to stdout with
--stdout)

Options:
  --record-id <id>  Record ID
  --file-id <id>    The record file id ("id" in list-record-files) or the
                    stored file id ("file_id")
  --out <path>      Destination file, or an existing directory to save into
                    under the original file name (default: ./<file name>)
  --stdout          Write the file contents to stdout instead of saving
```

## Notes

Downloads one file from a record's Files tab, as the file's Download does in the web app. It changes nothing on the record.

**Examples**

```
download-record-file --record-id 6aad6bfbd7f23b3a293e4aa7 --file-id 6ac541e60081b8686b3e0b41
download-record-file --record-id 6aad6bfbd7f23b3a293e4aa7 --file-id c442dc15-3727-4fb4-935a-1148a949b950
download-record-file --record-id 6aad6bfbd7f23b3a293e4aa7 --file-id 6ac541e60081b8686b3e0b41 --out "Terms 26-0095.pdf"
```

- `--file-id` takes the record file `id` from `list-record-files` (24 hex); the stored file's UUID (an email attachment's `file` id) works too. The file must be in that record's list, which includes linked records' files when the relationship includes them.
- `--out` is a file name, or a folder that already exists to save into under the file's own name (a folder that doesn't exist yet is taken as a file name). Without it the file is saved under its own name.
- `--stdout` writes the file's bytes into the output instead of saving it; useful only for small text files (CSV, TXT, HTML) whose content you want to read.

**Output:** one line, `Saved Terms Sheet 26-0095-1.pdf (130068 bytes, application/pdf) to <where>`. `--quiet` prints only where it was saved.

On PATHwd the saved file comes back as a download link in the result (in place of the path): give the link to the user. It needs their PATH sign-in and works for 24 hours. The file isn't kept in the workspace afterwards, so it can't be read or re-uploaded from there; to read a text file's content, use `--stdout`. `--out` takes a relative path.

**Common errors**

- `File <id> is not attached to record <record id>. Run "path list-record-files --record-id <record id>" to see its files.`: a wrong id, or a file on another record; list the record's files.
- `File server returned <status> for <file name>: …`: the stored file couldn't be fetched; try again, and report it if it persists.
- `<user> can't download a file from record … — they don't have View permission on <business object>, as in the web app.`

**Related:** `list-record-files`, `upload-record-file`, `send-record-email --record-file` (send the file by email instead).

Related commands: [`list-record-files`](list-record-files.md), [`list-record-notes`](list-record-notes.md), [`list-record-messages`](list-record-messages.md), [`list-record-emails`](list-record-emails.md), [`list-record-tasks`](list-record-tasks.md), [`get-history-by-record`](../../../pathwd-history/references/commands/get-history-by-record.md), [`get-history-session`](../../../pathwd-history/references/commands/get-history-session.md), [`get-event-metadata`](../../../pathwd-history/references/commands/get-event-metadata.md).
