# Workflow: record-activity

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../commands.md) gives any command's syntax and the tool that runs it.

**Covers:** the activity on one record: messages, notes in Notes fields, emails (reading and sending), files and tasks. Commands: `list-record-messages`, `send-message`, `list-record-notes`, `add-record-note`, `list-record-emails`, `send-record-email`, `list-record-files`, `upload-record-file`, `download-record-file`, `list-record-tasks`, `create-task` and their update and delete counterparts. Each command's syntax and output: its command note.

The account and session: the [`path-session` guide](../session.md). Every command's syntax: the [`path-cli` guide](../commands.md).

## What to ask the user

**Required:**
1. **Which record** — a record ID or enough info to find it (e.g. "Job 12345", "the Smith invoice")
2. **What action** — what they want to do (post a message, view files, complete a task, etc.)

**For posting a message:**
- **Message text** — what to say

**For adding a note:**
- **Note text** — and which Notes field, when the record has more than one

**For editing or deleting a note:**
- **Which note** — the user's own note (by text or date; you'll look up its id)

**For completing a task:**
- **Which task** — the task name (you'll look up the task ID)

**For changing, archiving or deleting a file:**
- **Which file** — by name or type (you'll look up the file ID)
- For a type change: **the new file type(s)**

**For sending an email from a record:**
- **To, subject and body** — and any CC / BCC or attachments

**For editing or deleting a message:**
- **Which message** — the user's own message (by text or date)
- For an edit: **the new text**

**Optional (use defaults if not mentioned — do NOT ask):**
- File comment → keep the current one

If the user hasn't specified an account ID, ask which account to use or run the [`path-session` guide](../session.md).

## Steps

### Step 1: Get the Record

If the user gave a record ID, fetch it directly. Otherwise, search for it.

```
# By ID
--account <ID> get-record --record-id <id>

# By search
--account <ID> search --query "<text>"
```

If the record isn't found on the first search, try other spellings and forms before asking the user (see "When a record can't be found" in the [`cli-records` workflow](records.md)). `get-record` also lists the record's open tasks (`Open tasks on this record`, each with its context and the pathway completing it runs) and the pathways it can start (`Pathways you can start on this record`). When the user asks for something one of them does, offer that first (see "First: is there a pathway or task for this?" in the [`cli-records` workflow](records.md)).

### Step 2: Perform the Action

#### Posting a Message

```
--account <ID> send-message \
  --record-id <id> \
  --message "The materials have arrived and are ready for installation."
```

To @mention people and send them a notification (what typing `@Name` in the record viewer's message box does), add `--to` with comma-separated user ids, emails or full names. Each one gets a mention chip in front of the text and a notification:

```
--account <ID> send-message \
  --record-id <id> \
  --message "Can you confirm the install date?" \
  --to "Jane Smith, mike@example.com"
```

#### Listing Messages

```
--account <ID> list-record-messages --record-id <id>
```

#### Listing Emails

```
--account <ID> list-record-emails --record-id <id>
```

#### Listing Files

```
--account <ID> list-record-files --record-id <id>
# only files of given types (ids and/or names, comma-separated)
--account <ID> list-record-files --record-id <id> --file-type "Import Set"
```

#### Downloading a File

```
# --file-id takes the record file id (the id column of list-record-files) or the stored file's UUID (an email attachment's file id)
--account <ID> download-record-file --record-id <id> --file-id <id> --out ./downloads/
--account <ID> download-record-file --record-id <id> --file-id <id> --stdout
```

The file is saved in this connection's workspace on PATH WD's server, and the result gives a download link: pass the link to the user. `--stdout` prints the contents instead, for a text file you need to read.

#### Changing a File's Type or Comment

A file's types are the categories it's filed under on the record (e.g. "Permit", "Signed Contract"). List the BO's file types with `list-file-types --bo-name <name>`.

```
# Find the file: use its "id"
--account <ID> list-record-files --record-id <id>

# Set its file types and comment
--account <ID> update-record-file \
  --record-id <id> \
  --file-id <record_file_id> \
  --data '{"file_type_ids": [412], "comment": "Signed by the city on 3/14"}'
```

- `file_type_ids` replaces the file's types: include every type the file should keep.
- Send the current `comment` text back if it shouldn't change: **leaving `comment` out erases it.**
- If a type is **exclusive** (only one file per record can have it), giving it to this file takes it off the file that had it.

#### Archiving or Unarchiving a File

Archiving sets a file aside as archived without deleting it. It can be unarchived later.

```
--account <ID> archive-record-file --record-id <id> --file-id <record_file_id>
--account <ID> unarchive-record-file --record-id <id> --file-id <record_file_id>
```

Archived files show `is_archived: true` in `list-record-files`.

#### Deleting a File

Deleting a file removes it from the record permanently. It can't be recovered. Confirm first, naming the file:

> "I'll permanently delete **site-photo-03.jpg** (Photos) from Job JOB-1234. This can't be undone. If you just want it out of the way, I can archive it instead. Delete it?"

Only continue on a clear yes. The command asks for confirmation too: the first run deletes nothing and answers `NOT DELETED`, naming the file and the record, with a code. Show that to the user and, only after their explicit yes, run the same command again with `--confirm <code>`:

```
--account <ID> delete-record-file --record-id <id> --file-id <record_file_id>
--account <ID> delete-record-file --record-id <id> --file-id <record_file_id> --confirm <code>
```

#### Uploading a File

`upload-record-file` uploads files from the user's computer to the record. Commands run on PATH WD's server, so the file comes in through `request_file_upload` first. It needs:

1. **The file, uploaded to the workspace.** Users expect to drop the file in the chat. If the client passed it as a file input (ChatGPT), call `attach_chat_file` once per file. Otherwise call `request_file_upload` with the file's name (and `multiple: true` for several files) and a short `purpose`; if you can run code where the file is (an attached file is usually in your sandbox's uploads folder), run its curl command there; if that fails or you can't, give the user the link right away (they can drag the file onto it) and wait until they say it's uploaded. Use the `uploads/<id>/<name>` path returned, and never say a file is uploaded before that's confirmed. The [`path-cli` guide](../commands.md)'s "Files on this connection" has the full order.
2. **A file type** from the record's business object: an id or a name from `list-file-types --bo-name <name>`. If the user didn't say which, show the types and ask.
3. Optionally a **comment**, added to each file.

Confirm the file names, the record and the file type, then (several paths can follow one `--file`, or each can have its own):

```
--account <ID> upload-record-file --record-id <id> \
  --file uploads/<id>/signed-contract.pdf uploads/<id>/site-photo.jpg \
  --file-type "Contracts" --comment "Signed at the site visit"
```

The result lists the new files. If a path is wrong the command stops before uploading anything and names the missing file.

#### Sending an Email from a Record

`send-record-email` **sends a real email**, exactly as Compose → Send on the record's Email tab does: it goes out from the user (their name and PATH email address) to the recipients given, and people outside PATH receive it. The record's own address is always CC'd, the user is CC'd too (leave that off with `--no-cc-me`), their default email signature follows the body (`--no-signature` leaves it off), and the sent email is saved to the record's Emails list. A sent email can't be recalled.

```
--account <ID> send-record-email --record-id <id> \
  --to john@smithplumbing.com --cc office@smithplumbing.com \
  --subject "Revised quote" \
  --body "Hi John, the revised quote is attached." \
  --record-file "Revised Quote.pdf" --preview
```

- `--to`, `--cc`, `--bcc`: comma-separated addresses (`name@example.com` or `"Name <name@example.com>"`). To, Subject and Body are required.
- `--body` is HTML or plain text (each line becomes a paragraph); `--body-file <path>` reads it from a file.
- `--record-file` attaches files already on the record: a record file id (the `id` column of `list-record-files`), the stored file's UUID, or the file name (comma-separated). `--file <paths...>` uploads files and attaches them (they're stored on the record as Email Attachment); a file from the user's computer gets its path the same way as for `upload-record-file`.
- `--preview` shows exactly what would be sent (from, recipients, subject, body with the signature, attachments) and sends nothing.

Always run it with `--preview` first, show the user the result and send only after they confirm:

> "I'll send this email from Job JOB-1234, from you (Maria Lopez):
> **To:** john@smithplumbing.com · **CC:** office@smithplumbing.com, the job's address and you · **Subject:** Revised quote · **Attachment:** Revised Quote.pdf
> Send it?"

Then run the same command without `--preview`. Report it as sent only when the command says "Sent"; any note it adds (email turned off for the account, or an account-wide recipient override) goes to the user too.

#### Editing or Deleting a Message

Only the author can change their own messages: the signed-in user can edit or delete messages they posted, not other people's. Find the message `id` with `list-record-messages`.

```
# Edit a message's text (replaces the whole text)
--account <ID> update-message \
  --record-id <id> \
  --message-id <message_id> \
  --data '{"message": "The materials arrive Thursday, not Wednesday."}'
```

To delete a message (only your own), run `delete-message`. It asks for confirmation: the first run deletes nothing and prints the record, the author, the date and the start of the message with a code. Show that to the user and, only after their explicit yes, run the same command again with `--confirm <code>`:

```
--account <ID> delete-message --record-id <record_id> --message-id <message_id>
--account <ID> delete-message --record-id <record_id> --message-id <message_id> --confirm <code>
```

The message disappears from the record's feed. Someone else's message is refused: only its author can delete it. Run `list-record-messages` again to check it's gone.

#### Notes Fields (reading, adding, editing, deleting notes)

A **Notes field** (e.g. "Job Notes") holds a running list of separate notes, each with its author and the time it was posted, newest first. `get-record` shows the newest 3 of each Notes field; `list-record-notes` shows them all with each note's id.

```
# Every Notes field you can see on the record, and all their notes (note ids, authors, "(you)" on yours)
--account <ID> list-record-notes --record-id <id>
# Just one Notes field (id or name)
--account <ID> list-record-notes --record-id <id> --field "Job Notes"

# Add a note (posted as the acting user, now). --field can be left out when the record has only one Notes field
--account <ID> add-record-note --record-id <id> --field "Job Notes" --note "Customer approved the revised layout."

# Change the text of a note (replaces the whole text) — only your own notes
--account <ID> update-record-note --record-id <id> --note-id <note_id> --note "Customer approved the revised layout on 10/26."

# Delete a note — only your own notes. Asks for confirmation: the first run deletes nothing and prints what it would delete and a code;
# only after the user explicitly says yes, run the same command again with --confirm <code>
--account <ID> delete-record-note --record-id <id> --note-id <note_id>
```

- Long text: `--note-file <path>` reads the note from a file instead of `--note`.
- **Who can do what (as in the web app):** adding needs Edit on the record's business object and the Notes field visible to the user in the record viewer; editing or deleting is for the note's **author only** (others get a refusal) — an account Admin can act as the author with `--as-user`.
- Notes are not regular field values: don't use `update-record` for them.
- To delete a note, quote it to the user and confirm first — a deleted note can't be recovered.

#### Listing Tasks

```
--account <ID> list-record-tasks --record-id <id>
```

#### Completing a Task

First list tasks to find the task ID, then complete it:

```
# List tasks to find the right one
--account <ID> list-record-tasks --record-id <id>

# Complete the task
--account <ID> complete-task \
  --record-id <id> \
  --task-id <task_id>
```

A task linked to a pathway isn't completed by `complete-task`: completing it in the web app runs that pathway, so the command is refused with the pathway's name and the command that runs it, `pathway-run start --record-id <id> --task-id <task_id>`. That run changes records (its screens save data, and a Complete Task step closes the task), so the user confirms before it starts. `get-record`'s `Open tasks on this record` names each task's pathway (`completing it runs pathway <id> "<name>"`).

#### Cancelling a Task

```
--account <ID> cancel-task \
  --record-id <id> \
  --task-id <task_id>
```

#### Creating a Task

Like the record viewer's **New Task**. Anyone who can open the record can create one. First see which task templates the record's current milestones offer (with each one's context):

```
--account <ID> create-task --record-id <id> --list-templates
```

Then create it from a template, or as a custom task:

```
# From a template (fills the title, assignee and settings; options change them)
--account <ID> create-task --record-id <id> --template "<id or title>" [--due tomorrow]

# Custom task
--account <ID> create-task --record-id <id> --title "Call customer" \
  --assign-to "<user id, email or full name>" --due 2026-11-12 --notes "..." \
  [--notify-me-when-done yes] [--notify-assignee-when-due yes] \
  [--can-be-assigned-by-others yes] [--can-be-skipped yes] [--only-assignee-can-finish yes]
```

- A template is accepted only when the record's current milestones offer it, as in the web picker. A record holds one open task per template, so a template that is already open on the record is refused (`--list-templates` marks it). Edit that task instead, or create a custom one.
- Dates are read on the user's clock: `YYYY-MM-DD`, `"YYYY-MM-DD HH:mm"`, `today`, `tomorrow`, `+3d`.
- Before creating, check the record's open tasks and pathways (`get-record`): a pathway or an existing task may already do what the user wants. Confirm the title, assignee and due date with the user first.

#### Editing a Task

Like a task's **Edit** button, on an open task:

```
--account <ID> edit-task --record-id <id> --task-id <task_id> \
  [--title ...] [--assign-to ...] [--due ...] [--notes ...] [--can-be-skipped yes|no] ...
```

- The task's **creator or an admin** can change every option.
- **Anyone else** can only change the assignee (`--assign-to`), and only when the task is set to "Can Be Assigned By Other Users". Otherwise the edit is refused. Say so in plain words ("only Maria, who created it, or an admin can change its due date").
- Pass only what changes; everything else stays as it is. `--due none` clears the due date, and `--assign-to none` unassigns it.

#### Assigning a Task

```
--account <ID> assign-task \
  --record-id <id> \
  --task-id <task_id> \
  --user "<name as list-users shows it, or email>"
```

Every task command takes the record id and the task id (`list-record-tasks` gives the ids).

### Step 3: Report Back

Tell the user what happened:

> "Done! I posted the message on Job JOB-1234. Here's what I sent:
> 'The materials have arrived and are ready for installation.'"

Or for task completion:

> "Done! I completed the **Review Quote** task on Job JOB-1234."

## CLI Commands Used

| Command | Purpose |
|---------|---------|
| `get-record --record-id <id>` | Get record details and context |
| `search --query <text>` | Find a record by search term |
| `send-message --record-id <id> --message <text> [--to <users>]` | Post a message on a record; `--to` @mentions and notifies users (ids, emails or full names) |
| `list-record-messages --record-id <id>` | List messages on a record |
| `list-record-emails --record-id <id>` | List emails on a record |
| `list-record-files --record-id <id> [--file-type <ids-or-names>]` | List files attached to a record (optionally by file type) |
| `upload-record-file --record-id <id> --file <paths...> --file-type <id-or-name> [--comment <text>]` | Upload local files to a record |
| `download-record-file --record-id <id> --file-id <id> [--out <path>] [--stdout]` | Download a record file |
| `list-record-notes --record-id <id> [--field <id\|name>]` | List a record's Notes fields and every note in them (newest first) |
| `add-record-note --record-id <id> [--field <id\|name>] --note <text>` | Add a note to a Notes field |
| `update-record-note --record-id <id> --note-id <id> --note <text>` | Edit the user's own note |
| `delete-record-note --record-id <id> --note-id <id> [--confirm <code>]` | Delete the user's own note (asks for confirmation) |
| `list-record-tasks --record-id <id>` | List tasks on a record |
| `complete-task --record-id <id> --task-id <id>` | Mark a task as complete |
| `cancel-task --record-id <id> --task-id <id>` | Cancel a task |
| `assign-task --record-id <id> --task-id <id> --user <name\|email>` | Assign a task to a user |
| `create-task --record-id <id> [--template <id\|title> \| --title <t>] [--assign-to …] [--due …] [--notes …]` | Create a task (New Task); `--list-templates` lists the templates offered |
| `edit-task --record-id <id> --task-id <id> [--title …] [--assign-to …] [--due …] [--notes …] …` | Edit an open task (creator/admin: everything; others: the assignee only, when allowed) |
| `update-record-file --record-id <id> --file-id <id> --data <json>` | Change a file's types and comment |
| `archive-record-file` / `unarchive-record-file --record-id <id> --file-id <id>` | Hide / restore a file |
| `delete-record-file --record-id <id> --file-id <id> [--confirm <code>]` | Permanently delete a file (asks for confirmation) |
| `send-record-email --record-id <id> --to <addresses> --subject <text> --body <text> [--cc …] [--bcc …] [--record-file …] [--file …] [--preview]` | Send a real email from the record, as you (Compose on the Email tab) |
| `update-message --record-id <id> --message-id <id> --data <json>` | Edit the user's own message |
| `delete-message --record-id <id> --message-id <id> [--confirm <code>]` | Delete the user's own message (asks for confirmation) |

If no PATH CLI command covers what the user asks for, tell them it can't be done here ([`path-cli`](../commands.md) lists every command).

## Key facts and confirmations

- Every command here runs through the PATH WD Remote MCP tool for its area (each command's page in [the command index](../commands.md) names it).
- Users name tasks, files and notes ("the Review Quote task"); their ids come from `list-record-tasks`, `list-record-files` and `list-record-notes`, and users never need to see them.
- A completed or cancelled task can't be reopened with these commands, and completing a task that has a pathway means running that pathway: the user confirms first.
- Lists read best with dates (on the user's clock), authors and the details that matter.
- A record that a search didn't find is searched again with other spellings and forms ("When a record can't be found" in the [`cli-records` workflow](records.md)) before asking the user, saying what was searched.
- Deleting a file, note or message is permanent and asks for a confirmation code; the user sees exactly what will be deleted and says yes first. Archiving a file keeps it and can be undone.
- `send-record-email` sends a real email that can't be recalled: `--preview` shows exactly what will go out and to whom, and the user approves the recipients and content before the send.
- File commands (update, archive, unarchive, delete) take the record file id from the `id` column of `list-record-files`; `download-record-file` also accepts the stored file's UUID (an email attachment's file id).
- Uploads take the `uploads/<id>/<name>` paths from `attach_chat_file` or `request_file_upload`; the user confirms the files, the record and the file type before `upload-record-file`.
