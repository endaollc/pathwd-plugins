# Errors and what to do about them

What a failed PATH CLI command prints, what each kind of message means, and the usual way forward. The message tables are generated from the CLI's and the remote MCP's source, so they list the actual wording (`…` stands for a name, an id or a value). Errors in Logic Builder formulas have their own table: the "Common mistakes" table in [find-records-syntax.md](../../../pathwd-find-records/references/find-records-syntax.md), generated from the formula parser's own cases.

## How an error reads

On PATHwd a failed command's result reads `Error (exit <code>): <message>` and is marked as an error. Notes a successful command prints beside its output (a count of what was left out, a warning) come after the output in the same result.

| Exit code | Meaning | Looks like |
|---|---|---|
| 1 | The command ran and failed: a refusal, a name that matched nothing, a bad value, a server error | `ERROR: …`, `ERROR [<status>]: …`, `<user> can't <action> — <why>, as in the web app.`; a command line the CLI can't read: `error: unknown option '--bogus'` |
| 2 | Refused before it ran (PATHwd): the wrong tool, a command that isn't offered, quotes that don't balance, a path outside the workspace | the remote MCP's own message (table below) |
| 3 | A delete waiting for confirmation (nothing was deleted), `execute-pathway` on a pathway with screen steps (nothing ran), or an agent credential's policy refusing the command | `NOT DELETED: this needs confirmation.`, `NOT RUN: Pathway <id> "<name>" has a step that shows a screen in the web app. …` |
| 4 | `pathway-run start` / `submit --strict`: a screen had no scripted answer | the screen that needs an answer |
| 5 | A limit of PATHwd: too many commands, the server busy, another command still running on the connection | `Too many commands: slow down (per-connection limit).` |

A command that failed with exit 1 or 2 usually changed nothing. The exceptions: a command that does several things in turn (`archive-record --record-ids`, several files in one `upload-record-file`) keeps what it finished before the failure; a command stopped by the time limit may have partly run; and in a pathway run each step that ran before the failure stays done (`pathway-run show` says where it stopped).

## The command line

| Message | Meaning |
|---|---|
| `error: unknown option '--bogus'` | The command has no such flag; its note and `<command> --help` list the ones it has. |
| `error: required option '--record-id <id>' not specified` | A required flag is missing. |
| `error: missing required argument '…'` / `error: too many arguments` | A positional value (`set-appointment <record-id>`, `get-report <report-id>`) is missing, or an extra word crept in (an unquoted value with spaces). |
| `error: option '--query <text>' argument missing` | A flag without its value. |
| `ERROR: Expected property name or '}' in JSON at position 1 …` (and other JSON messages) | A `--data`, `--user-filter` or `--answers` value isn't valid JSON: single-quote the whole value, double-quote every key and string. |
| `ERROR: --data takes {"fields":[{"field_id":<id>,"value":<stored value>}]} …` | `create-record` / `update-record` `--data` has the wrong shape (field names instead of ids, `fields` not an array): field ids are the `id` column of `list-fields --bo-name <bo>`. |

Quotes in a command string (the PATH plugin and PATHwd split the command line as a shell does):

<!-- generated:quote-errors -->
| Message | Meaning |
|---|---|
| `The command string's quotes don't balance — an apostrophe inside a single-quoted value is written '\'' (e.g. 'O'\''Brien'); or double-quote the value` | An apostrophe inside a single-quoted value (`'O'Brien'`) ends it early: write `'O'\''Brien'`, or double-quote the value. |
| `The command string has an unclosed " quote — a " inside a double-quoted value is written \" (e.g. "[Name] = \"Acme\""); or single-quote the value` | A double quote inside a double-quoted value: escape it as `\"`, or single-quote the value (formulas: `--logic '[Name] = "Acme"'`). |
<!-- /generated:quote-errors -->

## Before the command runs (PATHwd)

| Message | Meaning |
|---|---|
| `"<command>" is run by <tool>, not <this tool>.` | Each tool runs its own commands; the message names the right tool. |
| `args start with the command "<command>": pass "<command>" as command (args hold only its flags and values).` | The command's name went into `args`. |
| `Unknown command "…" for <tool>; its commands: …` | No such command on that tool. |
| `"<command>" is not available on PATHwd: …` | A command this connection doesn't offer (sign-in, sign-out, the raw API, pathway test commands). |

The service's own messages, from its source:

<!-- generated:remote-errors -->
| Message |
|---|
| `--base-url is not available here: the PATH API host is fixed for this connection.` |
| `… is outside the workspace. …` |
| `"…" already exists in the workspace (… bytes); it is kept. Pass overwrite: true to replace it, or choose another path.` |
| `"…" hasn't been uploaded yet: the file arrives through the link or the curl command request_file_upload returned. Nothing ran.` |
| `"…" is a folder in the workspace.` |
| `"…" is no longer available (uploads are kept for 24 hours). Request a new upload.` |
| `"…" is not a plain relative path in the workspace (uploads/ is reserved). …` |
| `"…" is not an upload of this connection: upload paths come from request_file_upload (or attach_chat_file).` |
| `Another command on this connection is still running. Wait for it to finish, then try again.` |
| `Content is … KB; write_workspace_file takes up to … KB (request_file_upload takes bigger files).` |
| `Reading from stdin ("--in -") is not available here. Pass a workspace file instead (request_file_upload or write_workspace_file).` |
| `Sign-in is done by connecting this MCP server (the connector sign-in), not with "auth". You are already signed in.` |
| `The PATH API host is fixed for this connection.` |
| `The server is busy; try again in a moment.` |
| `This agent credential may not run "…" (deletes are off).` |
| `This agent credential may not run "…" (outbound email is off).` |
| `This agent credential may not run "…".` |
| `This agent credential may not use --as-user.` |
| `This agent credential may not use super-user mode.` |
| `This agent credential reached its daily cap of … commands.` |
| `This agent credential's policy does not allow uploads.` |
| `This service is paused for your connection. Try again later or contact PATH support.` |
| `This tool runs "…"; the arguments resolve to "…".` |
| `To sign out, disconnect this MCP server in the app you connected it from: that revokes the PATH token. "logout" does nothing here.` |
| `Too many commands: slow down (per-connection limit).` |
| `Too many commands: slow down (per-user limit).` |
| `Too many failed agent credentials from this address; try again in a minute.` |
| `Too many uploads this hour.` |
<!-- /generated:remote-errors -->

- **A limit (exit 5)** refused the command before it ran: nothing changed. Wait a little (a few seconds; a minute for the rate limit) and send the same command again. In a bulk loop, slow down and report progress record by record.
- **Output and time limits** stop a command that is running: `ERROR: the command produced more than 1000000 bytes of output and was stopped. Narrow it down (--fields, --quiet, a page size).` and `ERROR: the command took longer than 120s and was stopped. It may have partly completed; check the current state before retrying.` Limits in full: [ids-names-paging-limits.md](ids-names-paging-limits.md).

## The account and the sign-in

| Message | Meaning |
|---|---|
| `ERROR: Missing --account <id>. Run "path list-accounts" to see available accounts.` | No account saved and none given: `use-account <id>` saves one, `--account <id>` picks one for a command; `list-accounts` lists them. |
| `ERROR: Account … isn't one of your accounts. Run "path list-accounts" …` | A wrong account id, or one the user doesn't belong to. |
| `ERROR: You're deactivated in account … (…), so you can't use it.` | The user's membership there is deactivated. |
| `ERROR [401]: Unauthorized` | The sign-in has expired or was revoked. The user signs in again (remotely: reconnects PATHwd in the app they connected it from). |
| `ERROR: Couldn't reach PATH at <host>: …` | The network or PATH is down; try again shortly. |

## Names that match nothing, or several things

Names are matched whole, ignoring case. A name that matches nothing gets up to three close names (`— did you mean "…"?`), or, with nothing close, the command that lists them; several matches are listed with their ids, and the id form of the flag (`--bo-id`, `--folder-id`, `--pathway-id`) picks one. A business object also matches by its plural name. A name created moments ago in the web app: `clear-cache`, or `--no-cache` on the command.

<!-- generated:name-errors -->
| Message |
|---|
| `Ambiguous business object name "…". Matches: …` |
| `Ambiguous field name "…". Matches: …` |
| `Ambiguous folder name "…". Matches: …` |
| `Ambiguous milestone name "…". Matches: …` |
| `Ambiguous pathway name "…". Matches: …` |
| `Ambiguous relationship name "…". Matches: …` |
| `Ambiguous step name "…". Matches: …` |
| `Business object "…" not found — did you mean "…"?` |
| `Field "…" not found on … — did you mean "…"?` |
| `Folder "…" not found on … — did you mean "…"?` |
| `Milestone "…" not found on … — did you mean "…"?` |
| `Pathway "…" not found — did you mean "…"?` |
| `Relationship "…" not found on … — did you mean "…"?` |
| `Step "…" not found on … — did you mean "…"?` |
<!-- /generated:name-errors -->

Other commands word the same thing their own way: `Report "…" not found`, `Unknown calendar "…". Calendars: …`, `No user "…" in account …`, `Attachment not found: no file "…" on this record …`. A user or role given to `create-folder` / `create-report` that matches nothing stops the command before anything is created: `Role "Outsde Sales" not found — did you mean "Outside Sales"? No folder was created.` (several: `Users not found: "…" (did you mean "…"?), "…" (session-context lists them). No report was created.`).

## Refusals: "can't … as in the web app"

`<user> can't <action> — <why>, as in the web app.` means the web app wouldn't let the acting user do it either. Nothing changed. The answer to the user is the `<why>` in plain words, and who could change it (an admin of their roles, the owner of a report or calendar, the author of a note). No other command gets around it. Why each rule applies and how to check it: [access-and-visibility.md](access-and-visibility.md).

Every reason the CLI gives:

<!-- generated:access-reasons -->
| `<why>` |
|---|
| `… isn't visible to them in the record viewer` |
| `… visible to them in the record viewer` |
| `a record they create is theirs` |
| `it belongs to an external (Google / Office 365) calendar and is read-only in PATH` |
| `it hasn't been shared with them` |
| `message … isn't on record …` |
| `none of their roles has the Delete Files permission` |
| `none of their roles has the User Admin (or Admin) permission` |
| `only Admins and Calendar Admins create calendars and calendar groups` |
| `only its creator or an admin can change its other options` |
| `only the …'s owner or an Admin / Calendar Admin can manage … "…"` |
| `only the message's author can change it` |
| `only the note's author can edit or delete it` |
| `record … isn't visible to them` |
| `that is Designer work and none of their roles has the Designer permission` |
| `the …relationship (…) isn't visible to them in the record viewer` |
| `the "…" (…) Notes field isn't visible to them in the record viewer` |
| `the record is archived (its tasks are hidden)` |
| `the session is on no record, and none of their roles has the Designer, User Admin (or Admin) permission` |
| `the web's Users page needs Admin or User Admin` |
| `they can only view calendar "…" (no Edit permission)` |
| `they can only view calendar group "…" (no Edit permission)` |
| `they can't see calendar …` |
| `they can't view …` |
| `they can't view its business object` |
| `they don't have Archive permission on …` |
| `they don't have Create Folders permission on …` |
| `they don't have Create Reports permission on …` |
| `they don't have Edit permission on …` |
| `they don't have Edit permission on folder …` |
| `they don't have Edit permission on report "…"` |
| `they don't have View permission on …` |
<!-- /generated:access-reasons -->

Pathway launches read differently: `<user email> can't start "<pathway>" from <point>`, followed by the launch checks that failed; `pathway-run map --pathway-name "<name>" --record-id <id>` gives them for one pathway without running it.

## Server errors: `ERROR [<status>]: …`

The PATH server refused the request; its message follows the status, with validation details after ` - ` (`… - fields.0.value: {"isNumber": "…"}`).

| Status | Usual cause | Next |
|---|---|---|
| 400 / 409 / 422 | A value or option the server won't take: a value of the wrong type, an option that doesn't exist, a missing required value, a duplicate name, something that already exists | Read the message and the property it names; value forms: [field-values.md](field-values.md) |
| 401 | The sign-in expired | Sign in again (remotely: reconnect PATHwd) |
| 403 | The server's own permission check | As a refusal above: say what the user can't do |
| 404 | No such id in this account for this user: a wrong id, a record number given as an id, another account's id, a record outside the user's tags | Find the id again (`search`, `find-records`, the list command); check the account |
| 5xx | The server failed | Try once more; if it fails again, report the message and what was being done |

## Values

A pathway screen reads typed values the way the web form does (`pathway-run submit --set "Field=value"`); a value it can't read is refused with `✗ <why>` and the run stays on the same screen, with nothing on it saved. The reasons, from the CLI's source:

<!-- generated:field-input-errors -->
| Message |
|---|
| `… has no blank option` |
| `… is a checkbox: use true or false` |
| `… is an appointment field — use the appointment options` |
| `… needs a duration like "1h 30m" or seconds` |
| `… needs a number, got "…"` |
| `"…" is not an option for …. Options: …` |
| `Can't read "…" as a date. Use YYYY-MM-DD, "YYYY-MM-DD HH:mm", an ISO timestamp, now, today, tomorrow or +3d (times are in …).` |
| `Unknown time unit "…" in …` |
<!-- /generated:field-input-errors -->

Phone numbers need 7 or 10–12 digits, emails `name@domain.tld`, and a required field a value. `create-record` / `update-record` `--data` values are sent as stored values and not re-read: a wrong form (`50` in a Percent field) is saved as given, not refused, so read the record back with `get-record`.

## Deletes and confirmations

`NOT DELETED: this needs confirmation.` (exit 3) is the first step of every permanent delete: it names exactly what would be deleted and gives a code. After the user's explicit yes to that, the same command with `--confirm <code>` deletes. `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: the code belongs to a different command, account or user, or is over 20 minutes old; run the command again without `--confirm` for a new one.

## Pathway runs

- `NOT RUN: Pathway <id> "<name>" has <n> steps that show a screen in the web app. …` (exit 3, from `execute-pathway`): nothing ran. It lists the steps the headless runner would skip (a Yes/No question takes No); `pathway-run start` runs the pathway with those screens, and the same command with `--skip-screens` runs it headlessly anyway.
- `✗ <why>` after `pathway-run submit`: the answer was refused; the run is still on the same screen. The screen's `Next:` lines give what it takes ([pathway-screens.md](../../../pathwd-pathways/references/pathway-screens.md)).
- A failed step prints its name and message, and the run ends as failed: report both, with the record's state now (`get-record`).
- `Unknown surface "…" — the CLI runner needs updating`: a screen the CLI can't answer; the run is finished in the web app.
- Exit 4 with `--strict`: a scripted run reached a screen its `--answers` didn't cover; `pathway-run show` displays it.

## Formulas

`find-records --logic`, `parse-filter`, `set-filter` and `create-folder` / `create-report --logic` print the formula with `^` marks under the problem and a message that names the fix. Every common mistake with the parser's own message and the right form: the "Common mistakes" table in [find-records-syntax.md](../../../pathwd-find-records/references/find-records-syntax.md); the whole language: the same file and the [`path-logic-formula-syntax` guide](../../../pathwd-find-records/references/logic-formula-syntax.md).
