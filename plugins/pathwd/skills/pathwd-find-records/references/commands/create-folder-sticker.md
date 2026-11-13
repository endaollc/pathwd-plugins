# create-folder-sticker

Runs with: path_manage_folders_reports(command: "create-folder-sticker", args: "<flags>") (change).

```
Usage: create-folder-sticker [options]

Add a sticker of the folder's own (shown on its rows when its formula matches),
saved the way the folder form saves it. A folder with Standard stickers
switches to Custom: every business object sticker keeps showing (and stays
hidden where it was hidden). Needs Edit on the folder. Prints the new sticker's
id

Options:
  --folder-id <id>       Folder ID
  --folder-name <name>   Folder name (requires --bo-id or --bo-name)
  --bo-id <id>           Business object ID (for --folder-name)
  --bo-name <name>       Business object name (for --folder-name)
  --name <name>          Sticker name (unique among the stickers the folder
                         shows)
  --icon <icon>          FontAwesome 6 icon name from the designer's icon
                         picker, e.g. flag, star, triangle-exclamation,
                         circle-check (older names such as exclamation-triangle
                         are renamed)
  --color <color>        Hex colour (#RRGGBB) or a colour word mapped to the
                         designer palette, e.g. orange (#FB8C00), red
                         (#D3302F), green (#388E3C), dark green (#1A5E20), blue
                         (#2196F3), grey (#9E9E9E)
  --logic <formula>      When the sticker shows: a Logic Builder formula on the
                         folder's business object, e.g. '[Unpaid Balance] > 0'
                         (parsed and validated as set-filter folder-sticker
                         does) — see /path-logic-formula-syntax
  --context <text>       Context: what the sticker means and when it shows —
                         shown with it to people and AI agents ("" clears it;
                         at most 50,000 characters)
  --context-file <path>  Read the context from a text or markdown file (instead
                         of --context)
  --dry-run              Parse and validate, print the save body, but save
                         nothing
```

## Notes

Adds a sticker of the folder's own: an icon shown on the folder's rows whose record matches the sticker's formula (the web's folder form, Stickers). Business object stickers, shown on every folder of the business object, are Designer work.

**Examples**

```
create-folder-sticker --folder-id 21099 --name "Big Job" --icon flag --color orange --logic '[Total Sell With Tax] > 10000' --context "Jobs over $10,000: the manager reviews them first."
create-folder-sticker --folder-name "Unpaid Jobs" --bo-name Job --name "Overdue" --icon triangle-exclamation --color red --logic '[Remaining Invoice Balance] > 0 AND INMILESTONE("Unpaid Invoices")'
create-folder-sticker --folder-id 21099 --name "Retail" --icon star --color "#2196F3" --logic '[Department] = "Retail"' --dry-run
```

- **Standard vs Custom:** a folder starts with Standard stickers (its business object's stickers, all shown). Its first own sticker switches it to Custom: every business object sticker keeps showing (hidden ones stay hidden) next to its own. `reset-folder-stickers` goes back to Standard.
- `--name` must differ from every sticker the folder shows, own or inherited.
- `--icon`: a FontAwesome 6 name from the web's icon picker (`flag`, `star`, `circle-check`, `triangle-exclamation`); older names such as `exclamation-triangle` are renamed. `--color`: `#RRGGBB` or a colour word (red, orange, yellow, green, dark green, blue, purple, grey…).
- `--logic`: a Logic Builder formula on the folder's business object, validated as the Logic Builder does; no `?` and no entry dates. Syntax: the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md).
- `--context`: what the sticker means; shown with it to people and AI agents.
- Needs Edit on the folder.

**Output:** `Created sticker 3402 "Big Job" on folder 21099 "All My Jobs (ISS)": icon flag, color #FB8C00`, then `formula: …`, and `… stickers are now Custom …` when the folder switched. 3402 is the new folder sticker id the other sticker commands take (`--quiet` prints it alone).

**Common errors**

- `Folder 21110 "In Process" already shows a sticker named "Trade" (3395, its own); sticker names on a folder must be unique.`
- `Unknown icon "X": use a FontAwesome 6 name from the designer's icon picker (close: …).`
- `Unknown colour "X": give a hex colour (#RRGGBB) or a colour word (…).`
- `The --logic formula is empty: a sticker needs a rule saying when it shows.`; a parse error naming an unknown field.
- `<user> can't change the stickers of folder 21110 "In Process" — they don't have Edit permission on folder 21110, as in the web app.`

**Related:** `list-folders` (each folder's stickers), `update-folder-sticker`, `delete-folder-sticker`, `hide-folder-sticker`, `show-folder-sticker`, `reset-folder-stickers`, `set-filter folder-sticker`, `update-folder --data` (`sticker_context`).

Related commands: [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`set-filter`](set-filter.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
