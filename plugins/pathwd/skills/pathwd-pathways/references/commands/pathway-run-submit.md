# pathway-run submit

Runs with: path_run_pathway(command: "pathway-run submit", args: "<flags>") (change).

```
Usage: pathway-run submit [options]

Answer the paused screen (only what that screen allows) and continue the run

Options:
  --run <id>             Run ID (default: the most recent run)
  --set <key=value>      A field or form value, e.g. --set "Status=Won"
                         (repeatable) (default: [])
  --yes                  Answer a Yes/No question with Yes
  --no                   Answer a Yes/No question with No
  --select <items>       Pick listed item(s) by number or id, comma-separated
  --file <path>          Workspace file to upload / sign with (repeatable)
                         (default: [])
  --skip                 Use the screen's "continue without" option where it
                         has one
  --data <json>          Raw result exactly as the web screen would return it
                         (bypasses the screen's checks)
  --answers <file|json>  Scripted answers for the screens after this one
  --strict               Fail (exit 4) instead of pausing when a later screen
                         has no scripted answer
```

## Examples

- args: `--run <run-id> --set "Sell Price=12500"`

## Notes

Answers the screen a pathway run is paused on and continues the run. What each screen accepts is in the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [pathway-screens.md](../pathway-screens.md), and the paused screen prints its own `Next:` commands.

**Examples**

```
pathway-run submit --set "Deposit Amount=1500" --set "Deposit Method=Check"
pathway-run submit --yes
pathway-run submit --set search="Henderson" --select 1
pathway-run submit --set mode=create --set "Customer Name=Smith Plumbing"
pathway-run submit --file uploads/<id>/signed-contract.pdf --set file_type=Contract
pathway-run submit --skip
```

- `--set "<key>=<value>"` (repeatable): a field by name or id, or a screen key (`search`, `note`, `template`, `start`, `calendar`, …). Field values are typed text, read as the web form reads it (`50` in a Percent field is 50%, dates `YYYY-MM-DD` on the user's clock).
- `--select <n|id>[,…]`, `--yes` / `--no`, `--file <path>` (repeatable), `--skip` (the screen's "continue without").
- `--data '<json>'`: the raw screen result, only for a screen nothing else answers.
- `--run <id>`: another run than the most recent.
- Keys `act` takes (search, page, scan, cell edits) also work here: they apply first, then the screen continues.

**Output:** the steps that ran, then the next paused screen or how the run ended. A refused answer prints `✗ <why>` and the run stays on the same screen; nothing on it was saved.

**Related:** `pathway-run act` (act on the screen without continuing), `pathway-run show`, `pathway-run cancel`.

Related commands: [`pathway-run start`](pathway-run-start.md), [`pathway-run act`](pathway-run-act.md), [`pathway-run cancel`](pathway-run-cancel.md), [`execute-pathway`](execute-pathway.md), [`run-step`](run-step.md).
