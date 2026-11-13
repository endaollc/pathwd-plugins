# pathway-run show

Runs with: path_pathways(command: "pathway-run show", args: "<flags>") (read-only).

```
Usage: pathway-run show [options]

Show a run: the screen it is waiting on, or how it ended

Options:
  --run <id>  Run ID (default: the most recent run)
```

## Notes

Shows a pathway run: the screen a paused run is waiting on, with its `Next:` commands, or how an ended run finished. It changes nothing. Use it to see the screen again (after a break, a context compaction, or when unsure which run is current) before answering it with `pathway-run submit`.

**Examples**

```
pathway-run show
pathway-run show --run 3f2b8c1e-5d47-4a8e-9b1c-2e6f0a9d7c34
```

- A run id is the UUID `pathway-run start` printed (`(run <id>)` under every screen). `pathway-run list` lists saved runs, newest first, with status and the screen each is waiting on.
- Without `--run`: the run last started, answered, acted on or shown. Pass `--run` whenever more than one run may be open.
- A run belongs to the account and acting user it started with: showing, answering and acting on it use those, whatever account is saved now.
- Runs are kept on the server with this connection, so every chat on the connection shares them, and "the most recent run" can be one another chat started. They are not deleted from here.

**Output**

- Paused, text: `Pathway "<name>" (<id>) — run <id>`, the pathway's context, then the screen as `pathway-run start` printed it: `━━ Waiting for input · step "<title>" [<type>] · screen: <surface> ━━`, the screen's fields or list, and `Next:` with the exact commands this screen takes right now.
- The screen lists what answering it needs: fields with type, required, current value, every option and context; numbered results with their ids; file types and the default; the keys it takes. The [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [pathway-screens.md](../pathway-screens.md) explains every screen.
- Ended: `Run <id>: "<name>" finished` (or `cancelled`, or `failed — <message>`), with the pathway's context. `pathway-run log --run <id>` shows what it did.

**Common errors**

- `No pathway run in progress. Start one with "pathway-run start".`: no run has been saved yet.
- `Pathway run <id> not found. "pathway-run list" shows saved runs.`: a mistyped id, or a run kept somewhere else.

**Related:** `pathway-run submit`, `pathway-run act`, `pathway-run cancel`, `pathway-run log` (what the run did, step by step), `pathway-run list`.

Related commands: [`pathway-run available`](pathway-run-available.md), [`pathway-run map`](pathway-run-map.md), [`pathway-run list`](pathway-run-list.md), [`pathway-run log`](pathway-run-log.md).
