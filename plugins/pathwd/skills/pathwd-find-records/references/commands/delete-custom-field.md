# delete-custom-field

Runs with: path_delete_folder_report_items(command: "delete-custom-field", args: "<flags>") (remove).

```
Usage: delete-custom-field [options] <report-id> <field>

Remove a report Custom Field. Refused while a filter rule compares it — change
the filter first (set-filter report <id>). Asks for confirmation (--confirm
<code>).

Arguments:
  report-id   Report ID
  field       Custom Field name or column_key

Options:
```

## Examples

- args: `42 "Days Open"`

## Notes

Removes a Custom Field (a filter-only calculation or Time Between) from a report, permanently. It is refused while a filter rule still compares it.

**Examples**

```
delete-custom-field 6800 "ZZ Since PO Received"
delete-custom-field 6800 "ZZ Since PO Received" --confirm DEL-91C4B7E2
```

- Arguments: the report id, then the Custom Field's name (any case) or its `column_key`, from `get-report <id>`; `[unused]` there marks the ones no filter rule compares.
- **Confirmation:** without `--confirm` nothing is deleted. The answer starts `NOT DELETED: this needs confirmation.`, names the field and report (`Custom Field "ZZ Since PO Received" on report 6800 "ZZ Related Time Between Check (#6266)"`) and gives a code. The user confirms; then the same command again with `--confirm <code>`. The code works only for that exact command line and expires within 20 minutes.
- **In use:** take the `CUSTOMFIELD("…")` rule out of the filter first: `get-filter report <id>`, then `set-filter report <id> --text '<formula without that rule>'`.
- Only Custom Fields; a report's shown columns are removed in the web app (the report's Edit).
- Needs Edit on the report; a user without it is refused before any code is given.

**Output:** `Deleted Custom Field "ZZ Since PO Received" from report 6800`.

**Common errors**

- `Custom Field "X" is used by the report filter; remove the CUSTOMFIELD("X") rule first.`
- `No Custom Field "X" on report 6800. Custom Fields: …`
- `NOT DELETED: confirmation code … doesn't match this delete (or has expired).`: run it again without `--confirm` for a new code.
- `<user> can't change report "…" — they don't have Edit permission on report "…", as in the web app.`

**Related:** `get-report`, `get-filter report <id>`, `set-filter report <id>`, `update-custom-field`, `create-custom-field`.

Related commands: [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md).
