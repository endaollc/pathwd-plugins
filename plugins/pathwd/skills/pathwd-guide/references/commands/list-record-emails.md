# list-record-emails

Runs with: path_record_activity(command: "list-record-emails", args: "<flags>") (read-only).

```
Usage: list-record-emails [options]

List emails on a record (newest first); --email-id shows one email in full,
body included

Options:
  --record-id <id>  Record ID
  --email-id <id>   Show this one email in full: addresses, attachments and the
                    body as text
  --page <n>        Fetch only this single page (default: all emails)
  --per-page <n>    Items per page (single-page mode) (default: "15")
  --all             Fetch all emails (this is now the default)
```

## Notes

The emails on a record's Emails list, newest first: emails sent from the record (the Email tab's Compose, a pathway's Send Email step, `send-record-email`) and emails received at the record's own address. Not the record's Messages (`list-record-messages`) or Notes fields (`list-record-notes`).

**Examples**

```
list-record-emails --record-id 6ad13092e59d243cf0810058
list-record-emails --record-id 6ad13092e59d243cf0810058 --email-id 6aec9ef23abd2d71dcc17e1d
list-record-emails --record-id 6ad13092e59d243cf0810058 --page 1 --per-page 5
```

- Every email is listed unless `--page` is given (then one page of `--per-page`, 15 by default).
- `--email-id` shows one email in full: every address, the attachments and the body as text.

**Output**

A table, newest first, then how to see one in full:

```
id                       | at               | dir | from                     | to                      | subject                            | att
-------------------------|------------------|-----|--------------------------|-------------------------|------------------------------------|----
6aeca0800f0da46f2fffb435 | 2026-11-05 09:27 | out | john@pathwd.com          | JOB1835@demo.pathwd.app | Send Email step test
6aec9ef23abd2d71dcc17e1d | 2026-11-05 09:21 | in  | claude-test@example.test | JOB1835@demo.pathwd.app | Inbound test 1                     | 1
[2 emails]
One in full (body, cc, attachments): list-record-emails --record-id 6ad13092e59d243cf0810058 --email-id <id>
```

Columns to read: `id` (for `--email-id`), `at` (sent or received, the user's clock), `dir` (`out` sent from the record, `in` received at its address), `from` and `to` (addresses), `subject`, `att` (attachment count). The record's own address (`JOB1835@…`) is in `to` of a received email and in the CC of a sent one.

`--email-id`:

```
email: 6aec9ef23abd2d71dcc17e1d
subject: Inbound test 1
received: 2026-11-05 09:21
from: Claude Test <claude-test@example.test>
to: JOB1835@demo.pathwd.app
sent by: JC Admin
reply to: 6aec9f743abd2d71dcc183cb
attachments: inbound-attachment.txt [file 6aec9ef20f0da46f2fffa60a]
body: Inbound test: this email should show as an Email Received event in History.
```

`cc`/`bcc` lines appear when set; `sent by` is the PATH user who sent it (sent emails); `reply to` the email it answers, when known. Each attachment's `[file …]` id is the record file `download-record-file --file-id` takes (the files are on the record's Files tab too). A long body is indented under `body:`.

**Common errors**

- `Email <id> isn't on record …`: the `--email-id` is from another record; list the record's emails.
- `<user> can't read the emails of record … — they don't have View permission on <business object>, as in the web app.`
- `[0 emails]`: no emails on the record.

**Related:** `send-record-email`, `list-record-files`, `download-record-file`, `get-history-by-record --category emails_texts`.

Related commands: [`list-record-files`](list-record-files.md), [`list-record-notes`](list-record-notes.md), [`list-record-messages`](list-record-messages.md), [`list-record-tasks`](list-record-tasks.md), [`get-history-by-record`](../../../pathwd-history/references/commands/get-history-by-record.md), [`get-history-session`](../../../pathwd-history/references/commands/get-history-session.md), [`get-event-metadata`](../../../pathwd-history/references/commands/get-event-metadata.md).
