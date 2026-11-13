# create-folder

Runs with: path_manage_folders_reports(command: "create-folder", args: "<flags>") (change).

```
Usage: create-folder [options]

Create a folder with permissions (and its context: --context / --context-file)

Options:
  --bo-id <id>                     Business object ID
  --bo-name <name>                 Business object name (resolved to ID)
  --folder-name <name>             Folder name
  --roles <names>                  Comma-separated role names
  --role-permissions <perms>       Comma-separated: view,edit,delete (default
                                   view)
  --users <names>                  Comma-separated users: names as list-users
                                   shows them, or emails
  --user-permissions <perms>       Comma-separated: view,edit,delete (default
                                   view)
  --logic <formula>                Logic Builder formula, e.g. '[Department] =
                                   "Retail" AND INMILESTONE("Lead")' — see
                                   /path-logic-formula-syntax
  --logic-raw <json>               Raw GroupFilter JSON (escape hatch for edge
                                   cases)
  --map-relationship <name-or-id>  Relationship (name or ID) from this folder's
                                   BO to the account's Address BO; sets
                                   map_enabled and map_relationship_id
  --context <text>                 The folder's context: what it is for, who
                                   uses it, what its records have in common —
                                   shown with the folder to people and AI
                                   agents (at most 50,000 characters)
  --context-file <path>            Read the folder's context from a text or
                                   markdown file (instead of --context)
  --dry-run                        Parse/build the request bodies and print
                                   them, but create nothing
```

## Examples

- args: `--bo-name Job --folder-name "Lead Jobs" --logic 'INMILESTONE("Lead")' --roles Sales`

## Notes

Creates a folder: a saved list of one business object's records that meet its criteria, shared with roles and users. The full how-to (gathering the criteria and who sees it, confirming with the user) is the [`cli-folders-skill` workflow](../workflows/folders.md).

**Examples**

```
create-folder --bo-name Job --folder-name "Retail Jobs in Ordering" --logic '[Department] = "Retail" AND INMILESTONE("Ordering")' --roles "Outside Sales,Sales Manager" --context "Retail jobs being ordered, for the sales team."
create-folder --bo-name Job --folder-name "Unpaid Jobs" --logic '[Remaining Invoice Balance] > 0' --roles Accounting --role-permissions view,edit --dry-run
create-folder --bo-name Job --folder-name "Jobs by Department" --logic '[Department] = ? AND NOTINMILESTONE("Closed")' --roles "All Users"
```

- **Who may create:** Create Folders permission on the business object (as the web's New Folder).
- **Access:** only what `--roles` / `--users` grant; the creator isn't added (Admins and folder admins see every folder). With neither, the folder reaches no one else's menu. "Everyone" is the account's all-users role (`list-roles`). `--users` takes full names or emails.
- **Levels:** `--role-permissions` / `--user-permissions` take `view`, `edit`, `delete` (comma-separated), the same words as `create-report`. Every share grants view; `edit` adds changing it, `delete` adds deleting it. Any other word is refused.
- **Names are checked first:** every role and user is looked up before anything is created; one that matches nothing stops the command, and no folder is created.
- **Criteria:** `--logic` is a Logic Builder formula on the folder's business object; folders also take `?` (each viewer fills it in) and milestone/step entry dates. Syntax: the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md) and the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [find-records-syntax.md](../find-records-syntax.md); check the count first with `find-records --count`. No `--logic` = every record of the business object. `--logic-raw` takes a stored `filter_condition` (raw GroupFilter JSON), an escape hatch for edge cases.
- User filters (rows viewers change) and own stickers come after: `set-filter folder <id> --user-filter`, `create-folder-sticker`.
- The folder starts with the business object's default columns, Standard stickers, archived records left out.

**Output:** `Created folder 32601 "Unpaid Jobs" on Job (bo 5050)`, then `Shared with roles (view): Sales (4002)` / `Shared with users (…): …` for the shares. `--quiet` prints the new id alone. `--dry-run` prints the folder body and the permissions it would grant, creating nothing.

**Common errors**

- `<user> can't create a folder on Jobs (business object 5050) — they don't have Create Folders permission on Jobs (business object 5050), as in the web app.`
- `Role "Outsde Sales" not found — did you mean "Outside Sales"? No folder was created.` / `User "…" not found (session-context lists them). No folder was created.` / `Roles not found: "…" (did you mean "…"?), "…" (list-roles lists them). No folder was created.`: nothing was created; the close names are roles and active users of the account.
- `"Pat Kim" is the name of 2 users in this account; give that user's email address instead. No folder was created.`
- `--role-permissions takes view, edit, delete (comma-separated), not write.`
- An error ending `Folder name is not unique` (from the server): the business object already has a folder with that name.
- A parse error naming the unknown field, milestone or function: see "Common mistakes" and "When a name isn't found" in `find-records-syntax.md`.
- `Relationship "X" (ID: …) does not target the account's Address business object.` (`--map-relationship`).

**Related:** `list-folders`, `list-records --folder-id`, `update-folder`, `set-filter folder <id>`, `create-folder-sticker`, `web-link folder <id>`, `create-report` (columns, totals, grouping).

Related commands: [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
