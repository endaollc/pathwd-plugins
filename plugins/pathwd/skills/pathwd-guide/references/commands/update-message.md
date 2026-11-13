# update-message

Runs with: path_update_record(command: "update-message", args: "<flags>") (change).

```
Usage: update-message [options]

Update a message on a record

Options:
  --record-id <id>   Record ID
  --message-id <id>  Message ID
  --data <json>      JSON body with fields to update
```

## Notes

Replaces the text of a message on a record's Messages, as the message's Edit does in the web app. Only the message's author can edit it (the `by` column of `list-record-messages`).

**Examples**

```
update-message --record-id 6aa15f317c0cf38a06d4160b --message-id 6ab3cac1da02468af8deed0d --data '{"message":"Deposit received 2026-11-10; ready to order."}'
```

- `--message-id` is the message's `id` from `list-record-messages` (24 hex).
- `--data` is `{"message": "<new text>"}`; the text replaces the whole message. Mention chips in the old text are kept only if the new text includes them (copy them from the old `message`); editing notifies no one.

**Output:** one line, `Updated message 6af51d0a0f0da46f2fffc1b2 on record 6af4b6fbe1333c2cca9710e0`. `--quiet`: the message id.

**Common errors**

- `<user> can't edit message … — only the message's author can change it, as in the web app.`: post a new message instead.
- `<user> can't edit message … — message X isn't on record Y, as in the web app.`: a wrong id or record, or the message was deleted; list the messages. Any message on the record can be edited, however old.
- A JSON parse error: `--data` must be valid JSON in single quotes.

**Related:** `list-record-messages`, `send-message`, `delete-message`.

Related commands: [`update-record`](update-record.md), [`unarchive-record`](unarchive-record.md), [`unarchive-record-file`](unarchive-record-file.md), [`set-appointment`](set-appointment.md), [`update-record-note`](update-record-note.md), [`update-record-file`](update-record-file.md).
