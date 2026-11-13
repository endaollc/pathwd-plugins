# pathway-run cancel

Runs with: path_run_pathway(command: "pathway-run cancel", args: "<flags>") (change).

```
Usage: pathway-run cancel [options]

Cancel a paused run (like closing the web modal). Work already saved stays
saved.

Options:
  --run <id>  Run ID (default: the most recent run)
```

## Examples

- args: `--run <run-id>`

## Notes

Cancels a paused pathway run, like closing the pathway's window in the web app. Nothing is undone: records saved by earlier screens and automatic steps stay as they are, and the screen it was waiting on is left unanswered. Nothing is sent to PATH; the run is only marked cancelled.

**Examples**

```
pathway-run cancel
pathway-run cancel --run 3f2b8c1e-5d47-4a8e-9b1c-2e6f0a9d7c34
```

- Without `--run` it cancels the most recent run (the one last started, answered, acted on or shown). Pass `--run` with the id from the screen's `(run <id>)` line when more than one run may be open, so the right one is cancelled.
- A cancelled run can't be resumed; start the pathway again with `pathway-run start` (automatic steps before the first screen run again).
- Since cancelling sends nothing to PATH, a run left paused and a cancelled run leave PATH the same. Cancel when the user decides not to go on, so the run isn't answered later by mistake.
- To reverse what an earlier screen saved, change the records themselves (`update-record`, `unlink-record`, `archive-record`); the record's history (`get-history-by-record --record-id <id>`) shows what the run changed.

**Output:** `Run <id> cancelled.` (text in every format).

**Common errors**

- `Run <id> is already finished` (or `cancelled`, `failed`): only a paused run can be cancelled.
- `No pathway run in progress. Start one with "pathway-run start".` / `Pathway run <id> not found. "pathway-run list" shows saved runs.`

**Related:** `pathway-run show`, `pathway-run list`, `pathway-run log`, `pathway-run start`.

Related commands: [`pathway-run start`](pathway-run-start.md), [`pathway-run submit`](pathway-run-submit.md), [`pathway-run act`](pathway-run-act.md), [`execute-pathway`](execute-pathway.md), [`run-step`](run-step.md).
