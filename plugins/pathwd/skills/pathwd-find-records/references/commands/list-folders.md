# list-folders

Runs with: path_folders_reports(command: "list-folders", args: "<flags>") (read-only).

```
Usage: list-folders [options]

List the folders in the user's folder menu: id, business object, name. Under
the table: each folder's context (what it is for, who uses it; once per session
like all context), its stickers (folder sticker id, name, own or inherited from
which business object sticker, hidden; [Standard] / [Custom]) and each
sticker's context (a copy of a business object sticker with none of its own
shows the business object sticker's)

Options:
  --bo-id <id>      Filter by business object ID
  --bo-name <name>  Filter by business object name
  --page <n>        Fetch only this single page (default: all folders)
  --per-page <n>    Items per page (single-page mode) (default: "50")
  --all             Fetch all folders (this is now the default)
```

## Examples

- args: `--bo-name Job`

## Notes

Lists the folders the user has in the web app's folder menu, with each folder's context and stickers. It doesn't list records: `list-records --folder-id <id>` opens a folder.

**Examples**

```
list-folders --bo-name Job
list-folders --bo-id 5051
list-folders
```

- Every folder by default (all pages); `--page` / `--per-page` fetch one page instead.
- Only folders the user would see in the menu: View on the business object, the business object shown in the menu, and View on the folder (Admins and folder admins see every folder). Why one is missing: the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [access-and-visibility.md](../../../pathwd-guide/references/cli/access-and-visibility.md).
- Folder names are unique per business object, not across the account; a command given a folder name also needs its business object (`list-records --folder-name "Leads" --bo-name Job`).

**Output:** a table, then the folders' context and stickers:

```
id    | bo       | name
------|----------|---------------------------
20975 | Jobs     | All Jobs
21110 | Services | In Process
32156 | Admin    | Delivery Trucks
[270 folders]

Folder context:
  Delivery Trucks (32156): The truck names users pick on Delivery Trucks and on Appointments.

Stickers (folder id "folder": folder sticker id "name" (own | inherited <business object sticker id>, hidden)):
  21110 "In Process" [Custom]: 2736 "High Priority" (inherited 443), 3395 "Trade" (own)
  24 folders of Services (bo 5051) with [Standard] stickers: "High Priority" (inherited 443)

Sticker context:
  folder 21110 "In Process" sticker "Trade": Trade customers' jobs.
```

- Columns to read: `id` (what `list-records --folder-id`, `get-filter folder`, `update-folder` and the sticker commands take), `bo` (the business object's name), `name`. With `--bo-id` / `--bo-name` the `bo` column is left out and the footer names it: `[61 folders of Jobs (bo 5050)]`. One page (`--page`): `[50 of 326 folders — --page 2 for more]`.
- Context: each folder's own (what it is for, who uses it), once per session.
- Stickers: a folder with Custom stickers on a line of its own; folders with only their business object's stickers (Standard, all shown) summed up per business object. The folder sticker id is what `update-folder-sticker`, `delete-folder-sticker`, `hide-folder-sticker` and `show-folder-sticker` take (a sticker's name, or an inherited sticker's business object sticker id, also works). A sticker's formula: `get-filter folder-sticker <folder sticker id>`.
- A folder's criteria and user filters: `get-filter folder <id>`.

**Common errors:** `Business object "X" not found — did you mean "…"?` (singular or plural name). An empty list means no folder of that business object is in the user's menu.

**Related:** `list-records` (a folder's records), `get-filter folder <id>`, `create-folder`, `update-folder`, `set-filter folder <id>`, `create-folder-sticker`, `web-link folder <id>`.

Related commands: [`list-reports`](list-reports.md), [`get-report`](get-report.md), [`run-report`](run-report.md).
