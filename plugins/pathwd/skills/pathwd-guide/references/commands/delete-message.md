# delete-message

Runs with: path_remove_from_record(command: "delete-message", args: "<flags>") (remove).

```
Usage: delete-message [options]

Delete a message on a record (your own). Asks for confirmation (--confirm
<code>)

Options:
  --record-id <id>   Record ID
  --message-id <id>  Message ID
```

## Notes

Deletes a message from a record's Messages, as the message's Delete does in the web app. Only the message's author can delete it (the `by` column of `list-record-messages`). It can't be undone from the CLI or the web app.

**Examples**

```
delete-message --record-id 6aa15f317c0cf38a06d4160b --message-id 6ab3cac1da02468af8deed0d
delete-message --record-id 6aa15f317c0cf38a06d4160b --message-id 6ab3cac1da02468af8deed0d --confirm DEL-51C0A7E2
```

- `--message-id` is the message's `id` from `list-record-messages` (24 hex).
- **Two calls.** The first deletes nothing: it exits with code 3 and says what it would delete, with a confirmation code:

  ```
  NOT DELETED: this needs confirmation.
  It would permanently delete message 6ab3cac1da02468af8deed0d by Pat Lee on Sep 23, 2026 at 7:49 AM on Job 26-0079: mobile job 98a (6aa15f317c0cf38a06d4160b): "@Sam Ortiz Deposit received; ready to order." in account 1668.
  ```

  Show that to the user; once they say yes, run the same command again with `--confirm <code>`. The code works only for this exact command and arguments, and expires within 20 minutes.
- The message is marked deleted: it drops out of `list-record-messages` and the web app's Messages tab, and the record's History shows Message Removed.

**Output:** one line, `Deleted message 6af51d0a0f0da46f2fffc1b2 from record 6af4b6fbe1333c2cca9710e0`. `--quiet`: the message id.

**Common errors**

- `<user> can't delete message … — only the message's author can change it, as in the web app.`: this comes before any confirmation code.
- `<user> can't delete message … — message X isn't on record Y, as in the web app.`: a wrong id or record, or the message was already deleted. Any message on the record can be deleted, however old.
- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: run it without `--confirm` for a new code.

**Related:** `list-record-messages`, `update-message`, `send-message`.

Related commands: [`archive-record-file`](archive-record-file.md), [`delete-record-file`](delete-record-file.md), [`delete-record-note`](delete-record-note.md), [`add-record-note`](add-record-note.md), [`upload-record-file`](upload-record-file.md), [`send-message`](send-message.md).
