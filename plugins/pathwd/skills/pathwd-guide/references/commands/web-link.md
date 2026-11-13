# web-link

Runs with: path_account(command: "web-link", args: "<flags>") (read-only).

```
Usage: web-link [options] <target> <id-or-name>

Print the PATH web app link to a record, folder or report (the same link the
app opens and shares)

Arguments:
  target          What to link to (choices: "record", "folder", "report")
  id-or-name      Record id; folder or report id or name

Options:
  --bo <id|name>  Business object, to pick a folder or report name that several
                  share
  --tab <tab>     Record only: open this tab (main, files, messages, email,
                  history, pathways, addresses, appointments,
                  contactsAndPeople)
```

## Examples

- args: `record 6a9056455ff33756afba5084`

## Notes

The web app link to a record, a folder or a report: the same link the app opens and shares, to hand to the user so they can open it in PATH. It changes nothing.

**Examples**

```
web-link record 6a9056455ff33756afba5084
web-link record 6a9056455ff33756afba5084 --tab pathways
web-link folder "All Jobs"
web-link folder "My Leads" --bo Job
web-link report 2477
```

- `record` takes a record id (24 hex characters), never a record number ("Job 26-0050"): find the id with `search`. The record is read first, so a wrong id fails here instead of opening a blank page.
- `--tab` (records only) opens a tab of the record viewer: `main`, `files`, `messages`, `email`, `history`, `pathways`, `addresses`, `appointments`, `contactsAndPeople`.
- `folder` and `report` take an id or the exact name (case doesn't matter). When several share the name, `--bo` (business object id or name) picks one, or use the id from `list-folders` / `list-reports`.
- The link opens in the account the command ran in; the person opening it needs to be signed in to PATH and allowed to see what it points to.

**Output:** the URL alone, e.g. `https://pathwd.app/accounts/1668/dashboard?record=6a9056455ff33756afba5084` (a record opens in the record viewer on the dashboard; `…?record=<id>:pathways` with a tab), `…/dashboard/business-objects/5050/folders/20975`, `…/business-objects/5048/reports/2477`. Give it to the user as a link.

**Common errors**

- `Record <id> not found` (or `Couldn't load record <id>; check the id`): not a record id in this account.
- `Folder "X" not found` / `Report "X" not found`: check the name with `list-folders` or `list-reports`.
- `2 folders are named "X"; pass the id or --bo:` followed by each id and business object.
- `--tab only applies to record links`.

**Related:** `search` (a record's id from its name or number), `list-folders`, `list-reports`, `get-record`.

Related commands: [`list-accounts`](list-accounts.md), [`use-account`](use-account.md), [`get-account`](get-account.md), [`session-context`](session-context.md), [`get-profile`](get-profile.md), [`get-notification-settings`](get-notification-settings.md), [`list-users`](list-users.md), [`list-roles`](list-roles.md), [`clear-cache`](clear-cache.md).
