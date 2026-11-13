# pathway-run act

Runs with: path_run_pathway(command: "pathway-run act", args: "<flags>") (change).

```
Usage: pathway-run act [options]

A non-advancing action on the paused screen, e.g. a lookup search: --set
search=Acme [--set page=2]

Options:
  --run <id>         Run ID (default: the most recent run)
  --set <key=value>  Action input (repeatable) (default: [])
  --select <items>   Pick / toggle listed item(s) by number or id,
                     comma-separated
  --file <path>      Workspace file to upload (repeatable) (default: [])
  --yes              Confirm an action the screen asks to confirm (e.g.
                     removing a tracking label)
```

## Notes

Does something on the screen a pathway run is paused on without continuing the run: search a lookup, turn a page, edit a table cell, scan a barcode, pick files, fill an email. The run stays on the same screen; `pathway-run submit` continues it. Which keys each screen takes is in the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [pathway-screens.md](../pathway-screens.md), and the paused screen prints its own `Next:` lines.

**Examples**

```
pathway-run act --set search="Henderson"
pathway-run act --set page=2
pathway-run act --set "2.Quantity=4" --set "3.Quantity=1"
pathway-run act --set save=true
pathway-run act --run 3f2b8c1e-5d47-4a8e-9b1c-2e6f0a9d7c34 --select 3,1,2
```

- `--set "<key>=<value>"` (repeatable, applied in order): a screen key (`search`, `page`, `row`, `add`, `archive`, `save`, `to`, `subject`, `scan`, …) or a table cell as `"<row>.<Field>=<value>"` (row number or record id; only editable columns, only where the cell is shown on that row).
- `--select <n|id>[,…]` picks or toggles listed items; `--file <path>` uploads a file to the screen; `--yes` confirms an action the screen asks to confirm (removing a tracking label).
- `--run <id>`: another run than the most recent. Pass it when more than one run may be open.
- The same keys work on `pathway-run submit`, which applies them first and then continues: `pathway-run submit --set search=Henderson --select 1` searches and picks in one command.

**Not continuing is not the same as not saving.** Some actions change PATH at once, as their buttons do in the web app: a table's `add=true` creates a record, `archive=<row>` archives one, `save=true` saves the table's edits; Create Tracking Labels creates each label as it is added; scan screens save each scan's action record as it is made (the session's processing runs on submit). Searches, pages, row details, selections and typed cell values (until saved) change nothing. Cancelling the run afterwards doesn't undo what was saved.

**Output:** the screen again, with the action's result (search results numbered, `Added row 4 (<id>)`, the edited cell) and fresh `Next:` lines.

**Common errors**

- `✗ <why>` (exit 2), the run unchanged: the screen refused the action, e.g. `This table has no search box`, `Search first — this table requires a search before Add`, `"Quantity" isn't an editable column. Editable: …`, `Only one row can be selected here`.
- `The <surface> screen has no actions besides submit`: answer it with `pathway-run submit`.
- `Run <id> is finished, not waiting for input.` (or cancelled, failed): only a paused run takes actions; `pathway-run show` says how it ended.
- `--set needs key=value, got "<text>"`.

**Related:** `pathway-run submit`, `pathway-run show`, `pathway-run cancel`, `pathway-run log` (every action and server request).

Related commands: [`pathway-run start`](pathway-run-start.md), [`pathway-run submit`](pathway-run-submit.md), [`pathway-run cancel`](pathway-run-cancel.md), [`execute-pathway`](execute-pathway.md), [`run-step`](run-step.md), [`search`](../../../pathwd-guide/references/commands/search.md).
