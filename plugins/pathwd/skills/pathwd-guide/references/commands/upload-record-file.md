# upload-record-file

Runs with: path_add_to_record(command: "upload-record-file", args: "<flags>") (add).

```
Usage: upload-record-file [options]

Upload one or more workspace files to a record

Options:
  --record-id <id>          Record ID
  --file <paths...>         Path(s) of the workspace file(s) to upload
  --file-type <id-or-name>  File type for the upload(s): an id or a name from
                            list-file-types on the record's BO
  --comment <text>          Comment to attach to each uploaded file
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --file uploads/<id>/quote.pdf --file-type Quote`

## Notes

Adds one or more files to a record's Files tab with a file type, as the Files tab's upload does (uploaded by the acting user, now).

**Examples**

```
upload-record-file --record-id 6aad6bfbd7f23b3a293e4aa7 --file uploads/k3v9q2/site-photo.jpg --file-type "Drawings/Plans"
upload-record-file --record-id 6aad6bfbd7f23b3a293e4aa7 --file uploads/k3v9q2/plan-a.pdf uploads/k3v9q2/plan-b.pdf --file-type 15858 --comment "Revised plans from the homeowner"
```

- `--file-type` is required: one type for every file in the call, an id or a name (case-insensitive) of the record's business object's types (`list-file-types --bo-name Job --include-hidden`). A name is checked against that list; an id is sent as given.
- `--file` takes several paths after it; put the other flags before or after the list, not inside it. The file's extension sets its content type (`.pdf`, `.jpg`, `.docx`, `.xlsx`, `.csv`, …).
- `--comment` is stored on each file.
- An exclusive file type is held by one file on the record at a time: uploading a new file of that type takes the type off the record's older file. The business object's file-upload notifications can go out.
- A file can't be replaced in place: upload the new one, then archive or delete the old one.

On PATH WD Remote MCP `--file` takes a path in the workspace, not a path on the user's computer. A file from the user comes in through `request_file_upload` (an upload link for the user, or a curl command) or, where listed, `attach_chat_file` (a file attached in the chat); each returns the workspace path, `uploads/<id>/<name>`. A small text file can be written with `write_workspace_file`. An uploaded file is used up by the first write that succeeds with it (a command that only reads it, a preview, a dry run or a failed run leaves it as it was); using it again after that takes a new upload.

- `Only files in the workspace can be used here: …`: the path leads outside the workspace (it starts with `/` or `~`, or holds `..`); bring the file in with `request_file_upload` first.
- `"uploads/…" hasn't been uploaded yet: …. Nothing ran.`: the user hasn't finished the upload (or the upload was already used by an earlier command).

**Output:** one line with each new record file's id and name, e.g. `Uploaded file 6af51c2e0f0da46f2fffc101 "site-photo.jpg" to record 6aad6bfbd7f23b3a293e4aa7 (file type Drawings/Plans)`, or `Uploaded 2 files to record … (file type 15858): <id> "plan-a.pdf", <id> "plan-b.pdf"`. `--quiet`: the new ids, one per line. `list-record-files` shows them with their type names.

**Common errors**

- `File not found: <path>` / `Not a file: <path>`: the path is wrong; nothing was uploaded.
- `Unknown file type "X". Known types: …`: use a name from the list (or `list-file-types`).
- `<user> can't upload a file to record … — they don't have View permission on <business object>, as in the web app.`

**Related:** `list-record-files`, `update-record-file` (type or comment), `archive-record-file`, `send-record-email --file` (upload and email in one step).

Related commands: [`add-record-note`](add-record-note.md), [`send-message`](send-message.md), [`archive-record-file`](archive-record-file.md), [`delete-record-file`](delete-record-file.md), [`delete-record-note`](delete-record-note.md), [`delete-message`](delete-message.md).
