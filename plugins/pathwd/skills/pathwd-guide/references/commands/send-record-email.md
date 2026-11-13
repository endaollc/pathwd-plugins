# send-record-email

Runs with: path_send_email(command: "send-record-email", args: "<flags>") (add).

```
Usage: send-record-email [options]

Send a real email from a record, as the record viewer's Email tab Compose →
Send does: it goes out from you (your name and PATH email address), the
record's own address is always CC'd, you are CC'd unless --no-cc-me, your
default email signature follows the body, and the sent email is saved to the
record's Emails list. Recipients outside PATH receive it. --preview shows
exactly what would be sent

Options:
  --record-id <id>              Record ID
  --to <addresses>              To: email addresses, comma-separated
                                (repeatable; required)
  --cc <addresses>              CC: email addresses, comma-separated
                                (repeatable)
  --bcc <addresses>             BCC: email addresses, comma-separated
                                (repeatable)
  --subject <text>              Subject (required)
  --body <html-or-text>         Body: HTML, or plain text (each line becomes a
                                paragraph)
  --body-file <path>            Read the body (HTML or plain text) from a file
                                (instead of --body)
  --record-file <ids-or-names>  Attach files already on the record:
                                list-record-files id, file_id or file name,
                                comma-separated (repeatable)
  --file <paths...>             Upload workspace file(s) onto the record (file type
                                Email Attachment) and attach them, as the
                                composer's upload does
  --no-cc-me                    Don't CC yourself (the composer prefills you as
                                a removable CC)
  --no-signature                Don't add your default email signature after
                                the body
  --preview                     Show the email that would be sent, without
                                sending or uploading anything
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084 --to pat@example.com --subject "Revised quote" --body "Hi Pat, the revised quote is attached." --record-file "Revised Quote.pdf" --preview`
- args: `--record-id 6a9056455ff33756afba5084 --to pat@example.com --subject "Revised quote" --body "Hi Pat, the revised quote is attached." --record-file "Revised Quote.pdf"`

## Notes

Sends a real email from a record, as Compose → Send on the record's Email tab does. It goes out from the acting user (their name and PATH email address) to the addresses given, people outside PATH included, and is saved to the record's Emails list. A sent email can't be recalled: the user confirms the recipients, subject and body (shown with `--preview`) before it is sent.

**Examples**

```
send-record-email --record-id 6aad6bfbd7f23b3a293e4aa7 --to pat@example.com --subject "Revised quote for 4100 McBrine Pl" --body "Hi Pat, the revised quote is attached." --record-file 6ac542c1dc615d8c41d30f0d --preview
send-record-email --record-id 6aad6bfbd7f23b3a293e4aa7 --to pat@example.com --subject "Revised quote for 4100 McBrine Pl" --body "Hi Pat, the revised quote is attached." --record-file 6ac542c1dc615d8c41d30f0d
send-record-email --record-id 6aad6bfbd7f23b3a293e4aa7 --to "Pat Lee <pat@example.com>, sam@example.com" --cc office@example.com --subject "Install on 2026-11-20" --body-file install-email.html --no-cc-me
```

- **Preview, confirm, send.** `--preview` composes the email exactly as Send would (all checks included) and sends and uploads nothing. Show its From, To, CC, BCC, subject, body and attachments to the user; after they say yes, run the same command without `--preview`.
- What is added on its own: the record's own email address is always CC'd (replies land on the record); the acting user is CC'd unless `--no-cc-me` (not twice when already a recipient); their default email signature follows the body unless `--no-signature`.
- `--to` is required; `--to`, `--cc`, `--bcc` take addresses separated by commas or semicolons, as `name@example.com` or `"Name <name@example.com>"`, and can be repeated. The sender can't be chosen.
- `--body`: HTML, or plain text where each line becomes a paragraph. `--body-file` reads it from a file. There are no merge fields: `[Field]` text is sent as typed, so read values with `get-record` and write them in.
- `--record-file` attaches files already in the record's `list-record-files` (its `id`, the stored file's UUID, or the file name, case-insensitive; comma-separated, repeatable). A name several files share is refused with their ids.
- `--file` uploads files onto the record (file type Email Attachment) and attaches them; the upload happens on send, not on preview.
- Account settings can change delivery, and the output's `Note:` lines say so: email turned off for the account (saved on the record, not delivered), or an email override (delivered only to the override address, CC and BCC dropped).

On PATH WD Remote MCP `--file` and `--body-file` take workspace paths (`request_file_upload`, `attach_chat_file` or `write_workspace_file` put files there). An uploaded file is used up by the first write that succeeds with it; a `--preview` sends and uploads nothing and leaves the upload as it was, so the preview and the send can name the same `uploads/<id>/<name>` path. A connection made with an agent credential sends email only when its policy allows it: `This agent credential may not run "send-record-email" (outbound email is off).`

**Output**

`--preview`: nothing is sent; each part on its own line, the body as text:

```
Preview: nothing sent. send-record-email would send this from record 6aad6bfbd7f23b3a293e4aa7:
  From:    JC Admin <jc@demo.pathwd.app>
  To:      pat@example.com
  CC:      JOB1835@demo.pathwd.app, jc@demo.pathwd.app
  BCC:     (none)
  Subject: Revised quote for 4100 McBrine Pl
  Signature: appended
  Attachments: Quote Rev 2.pdf
  Body (HTML, shown as text):
    Hi Pat, the revised quote is attached.
```

Sent: two lines, the email id first (`list-record-emails --email-id` takes it), then who got it:

```
Sent email 6aeca0800f0da46f2fffb435 "Revised quote for 4100 McBrine Pl" from JC Admin <jc@demo.pathwd.app> on record 6aad6bfbd7f23b3a293e4aa7
  to: pat@example.com; cc: JOB1835@demo.pathwd.app, jc@demo.pathwd.app; attachments: Quote Rev 2.pdf
```

`  Note: …` lines follow when account settings changed delivery. `Not sent: the only recipient was the record's own address` means nothing went out. `--quiet` (sent): the email id.

**Common errors**

- `Can't send: To is required (--to name@example.com)` / `Can't send: Subject is required (--subject)` / `Can't send: Body is required (--body or --body-file)`.
- `--to: "pat@" is not a valid email address (use name@example.com or "Name <name@example.com>"; separate several with commas)`.
- `Can't send: the only recipient is the record's own address, so the email would go nowhere. Add a recipient with --to.`
- `Attachment not found: no file "X" on this record (use an id, file_id or file name from list-record-files)` / `Attachment "X" matches 2 files on this record; use one of their ids: …` / `Attachment "X" is archived; the composer doesn't offer archived files. Unarchive it first.`
- `This business object has no Email Attachment file type`: `--file` can't be used on this record; upload with `upload-record-file` under another type and use `--record-file`.
- `send-record-email no longer takes --data: …`: use the flags above.
- `<user> can't send an email from record … — they don't have View permission on <business object>, as in the web app.`

**Related:** `list-record-emails`, `list-record-files`, `upload-record-file`, `send-message` (a message to teammates inside PATH), `add-record-note`.
