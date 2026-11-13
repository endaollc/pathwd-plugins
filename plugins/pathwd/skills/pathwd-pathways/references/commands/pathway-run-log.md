# pathway-run log

Runs with: path_pathways(command: "pathway-run log", args: "<flags>") (read-only).

```
Usage: pathway-run log [options]

A run's trace: steps, busy messages, every server request and reply

Options:
  --run <id>  Run ID (default: the most recent run)
  --api       Include full request inputs and outputs
```

## Notes

A pathway run's trace: when it started and with what launch checks, each step it reached, each screen it paused on, every answer and action given, every request to PATH with its result, and how it ended. Read-only. Use it to explain what a run did or why it failed, ended early or took an unexpected branch.

**Examples**

```
pathway-run log
pathway-run log --run 3f2b8c1e-5d47-4a8e-9b1c-2e6f0a9d7c34
pathway-run log --run 3f2b8c1e-5d47-4a8e-9b1c-2e6f0a9d7c34 --api
```

- Without `--run`: the most recent run (the one last started, answered, acted on or shown). Finished, failed and cancelled runs keep their log; `pathway-run list` finds older ones.
- `--api` adds each request's full input and output under its line, for a step whose result needs a closer look.

**Output**

- One line per event, time first (UTC):
  - `started {…}`: pathway, starting record, mode, launch point and each launch check.
  - `▸ step 3 Relationship Lookup Job Main Customer [relationship_lookup_record]`: a step that showed activity (a request, a screen), by its Designer number.
  - `⏸ create-record — "Enter Payment Details"`: a screen it paused on; `input {"phase":"act"|"submit", …}`: what was entered there.
  - `→ RelationshipLookup (186 ms)` a request to PATH; `✗ … <error>` a failed one.
  - `↩ user reply: …` what a screen returned; `ended {"outcome": {…}, "stats": {…}}`: how it ended.
- `--api` adds each request's full input and output (`in:` / `out:` under its `→` line); it is many times longer, so read the plain log first.
- A step number missing between `▸` lines was either passed over by a branch (Fork, found/not found, Yes/No) or ran without a request to PATH. `pathway-run map` shows where each branch leads.

**Common errors**

- `No pathway run in progress. Start one with "pathway-run start".` / `Pathway run <id> not found. "pathway-run list" shows saved runs.`

**Related:** `pathway-run show` (the screen it's on, or how it ended), `pathway-run list`, `pathway-run map`, `get-history-by-record` (what changed on the record, as the web app's History tab shows it).

Related commands: [`pathway-run available`](pathway-run-available.md), [`pathway-run map`](pathway-run-map.md), [`pathway-run show`](pathway-run-show.md), [`pathway-run list`](pathway-run-list.md).
