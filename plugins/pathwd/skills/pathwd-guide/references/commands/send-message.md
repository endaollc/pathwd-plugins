# send-message

Runs with: path_add_to_record(command: "send-message", args: "<flags>") (add).

```
Usage: send-message [options]

Post a message on a record

Options:
  --record-id <id>  Record ID
  --message <text>  Message text
  --to <users>      Comma-separated users to @mention and notify, as the web's
                    message box does: ids, emails or full names
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --message "Deposit received."`

## Notes

Posts a message on a record's Messages, as the record viewer's message box does: a conversation among PATH users, posted as the acting user. `--to` @mentions users, and each one mentioned gets a notification. Nothing leaves PATH; to email someone use `send-record-email`, and for a dated note in a Notes field use `add-record-note`.

**Examples**

```
send-message --record-id 6aad6bfbd7f23b3a293e4aa7 --message "Deposit received; ready to order."
send-message --record-id 6aad6bfbd7f23b3a293e4aa7 --message "Can you confirm the install date?" --to "Pat Lee"
send-message --record-id 6aad6bfbd7f23b3a293e4aa7 --message "Measurements are in the Files tab." --to "pat@example.com,12"
```

- `--to` takes users by name as `list-users` shows it, email or id (case-insensitive), comma-separated; a name two users share is refused, asking for the email. Each gets an `@Name` mention chip in front of the text and a notification, as in the web app; a message without `--to` notifies no one in particular.
- `--message` is the text as typed (HTML is kept as HTML).
- A message can be edited or deleted later only by its author (`update-message`, `delete-message`).

**Output:** one line, `Posted message 6af51d0a0f0da46f2fffc1b2 on record 6af4b6fbe1333c2cca9710e0, notified Ann Lee, Bob Ray` (the `--to` users; nothing after the record id without `--to`). The message id is what `update-message` / `delete-message` take. `--quiet`: the message id. The record's History shows it as Message Sent.

**Common errors**

- `No user "X" in this account (give their name as list-users shows it, their email or their id)`: check the spelling with `list-users` (or the session context's users).
- `"X" is the name of 2 users in this account; give that user's email address instead.`: ask the user which person they mean.
- `<name> is deactivated`: a deactivated user can't be mentioned.
- `<user> can't post a message on record … — they don't have View permission on <business object>, as in the web app.`

**Related:** `list-record-messages`, `update-message`, `delete-message`, `add-record-note`, `send-record-email`.

Related commands: [`add-record-note`](add-record-note.md), [`upload-record-file`](upload-record-file.md), [`archive-record-file`](archive-record-file.md), [`delete-record-file`](delete-record-file.md), [`delete-record-note`](delete-record-note.md), [`delete-message`](delete-message.md).
