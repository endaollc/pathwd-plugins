# update-folder-sticker

Runs with: path_manage_folders_reports(command: "update-folder-sticker", args: "<flags>") (change).

```
Usage: update-folder-sticker [options]

Change one of the folder's own stickers: name, icon, color, formula and/or
context (the rest as stored). An inherited business object sticker is changed
with the Designer CLI (update-sticker) or hidden here (hide-folder-sticker).
Needs Edit on the folder

Options:
  --folder-id <id>        Folder ID
  --folder-name <name>    Folder name (requires --bo-id or --bo-name)
  --bo-id <id>            Business object ID (for --folder-name)
  --bo-name <name>        Business object name (for --folder-name)
  --sticker <id-or-name>  The folder sticker: its id (list-folders) or name; an
                          inherited one also by its business object sticker id
  --name <name>           New name (unique among the stickers the folder shows)
  --icon <icon>           FontAwesome 6 icon name from the designer's icon
                          picker, e.g. flag, star, triangle-exclamation,
                          circle-check (older names such as
                          exclamation-triangle are renamed)
  --color <color>         Hex colour (#RRGGBB) or a colour word mapped to the
                          designer palette, e.g. orange (#FB8C00), red
                          (#D3302F), green (#388E3C), dark green (#1A5E20),
                          blue (#2196F3), grey (#9E9E9E)
  --logic <formula>       When the sticker shows: a Logic Builder formula on
                          the folder's business object, e.g. '[Unpaid Balance]
                          > 0' (parsed and validated as set-filter
                          folder-sticker does) — see /path-logic-formula-syntax
  --context <text>        Context: what the sticker means and when it shows —
                          shown with it to people and AI agents ("" clears it;
                          at most 50,000 characters)
  --context-file <path>   Read the context from a text or markdown file
                          (instead of --context)
  --dry-run               Parse and validate, print the save body, but save
                          nothing
```

## Notes

Changes one of the folder's own stickers: its name, icon, colour, formula or context; whatever isn't given stays as stored. A sticker inherited from the business object isn't changed here: it can be hidden on this folder (`hide-folder-sticker`), and the business object sticker itself is Designer work.

**Examples**

```
update-folder-sticker --folder-id 21110 --sticker Trade --color green --context "A trade customer: pricing follows the trade price list."
update-folder-sticker --folder-id 21110 --sticker 3395 --logic '[Department] = "Trade" AND NOTINMILESTONE("Closed")'
update-folder-sticker --folder-name "All My Jobs (ISS)" --bo-name Job --sticker ISS --name "Inside Sales" --icon user
update-folder-sticker --folder-id 21110 --sticker Trade --context ""
```

- `--sticker` takes the folder sticker id (from `list-folders`) or its name; an ambiguous name asks for the id.
- `--logic` replaces the whole formula (no `?`, no entry dates), validated as the Logic Builder does; `get-filter folder-sticker <folder id> --sticker <id>` prints the current one. `set-filter folder-sticker` does the same change.
- `--icon` / `--color` take the forms `create-folder-sticker` takes; a new `--name` must differ from every sticker the folder shows.
- `--context ""` clears the sticker's context.
- Needs Edit on the folder.

**Output:** `Updated sticker 3395 "Trade" on folder 21110 "In Process": color #388E3C, context (54 characters)` (and `formula: …` when it changed). If the server saved the sticker as a new row: `NOTE: the server saved it as a new row: its id is now 3410.`

**Common errors**

- `Sticker 2736 "High Priority" on folder 21110 is inherited from business object sticker 443: change it for every folder with the Designer CLI (…), or hide it on this folder with hide-folder-sticker.`
- `No sticker "X" on folder 21110 "In Process". Stickers: 2736 "High Priority", 3395 "Trade"`.
- `Nothing to change: pass --name, --icon, --color, --logic, --context or --context-file`.
- `Folder 21110 "In Process" already shows a sticker named "…" (…); sticker names on a folder must be unique.`
- `Unknown icon "X": …` / `Unknown colour "X": …` (see `create-folder-sticker`).

**Related:** `list-folders`, `create-folder-sticker`, `delete-folder-sticker`, `hide-folder-sticker`, `reset-folder-stickers`, `get-filter folder-sticker`.

Related commands: [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`create-folder-sticker`](create-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
