# execute-pathway

Runs with: path_run_pathway(command: "execute-pathway", args: "<flags>") (change).

```
Usage: execute-pathway [options]

Run a pathway on a record on PATH's headless runner, the way scheduled pathways
and integrations run: no screens, so it suits a pathway whose steps are all
automatic. The headless runner skips a step that shows a screen in the web app
(a Yes/No question takes No); when the pathway has such steps, nothing runs and
they are listed, unless --skip-screens is given. `pathway-run start` runs a
pathway step by step with its screens. Like a record's Pathways tab in the web
app, it only starts where the acting user could start it (role, shown on
records, starting business object, record access, the pathway's visibility
criteria)

Options:
  --pathway-id <id>      Pathway ID
  --pathway-name <name>  Pathway name (resolved to ID)
  --record-id <id>       Record ID to execute the pathway on
  --data <json>          Additional JSON body fields (e.g. {"task_id":123});
                         starting_record.id is set from --record-id
  --skip-screens         Run it even when it has steps that show a screen in
                         the web app; the headless runner skips them
  --mode <mode>          real-world (default) | super-user (also when the
                         pathway's visibility criteria don't match the record;
                         Designer only, this run only)
```

## Notes

Runs a pathway on a record on PATH's headless runner, the way scheduled pathways and integrations run: PATH runs its steps on the server with no screens and returns when the run ends. It suits a pathway whose steps are all automatic. The headless runner skips a step that shows a screen in the web app (Post Note, an Edit Record form, a Send Email screen, Upload File, …), so a pathway made of screens finishes without doing them. `pathway-run start` runs a pathway step by step with its screens. Like a record's Pathways tab, it starts only where the user could start it.

**Examples**

```
execute-pathway --pathway-name "Send Job to Accounting" --record-id 6a9056455ff33756afba5084
execute-pathway --pathway-id 25928 --record-id 6a9056455ff33756afba5084
execute-pathway --pathway-id 41675 --record-id 6a9056455ff33756afba5084 --data '{"task_id": 9071}'
execute-pathway --pathway-id 25927 --record-id 6a9056455ff33756afba5084 --skip-screens
```

- `--record-id` is required: the starting record. A pathway that starts without a record (dashboard, folder) is run with `pathway-run start --from dashboard`.
- The launch is checked as from the record's Pathways tab: role, "show on records", starting business object, record access, visibility criteria. `--mode super-user` (Designers, this run only) skips only the visibility criteria and prints `⚠ visibility criteria: … (super-user mode runs it anyway)`.
- `--data '{"task_id": <id>}'` runs it as completing that task: Complete Task steps close it, and the task's "only the assignee can finish" and "edit permission required" rules apply. `pathway-run start --task-id` does the same with screens.
- **Screen steps:** when the pathway has a step `pathway-run map` calls a screen (on any branch or loop a run can reach), nothing runs: the answer is `NOT RUN: …` (exit 3, as a delete waiting for confirmation), listing each such step with what the headless runner does with it instead. `--skip-screens` runs it anyway, on the headless runner. A pathway with no screen steps runs at once, without the flag. A `sometimes` step (an Auto Send email, a file pick that skips itself on one file) doesn't count. A pathway a Launch Pathway step runs isn't looked into: its screens are skipped the same way, unlisted.
- On the headless runner a screen step is skipped, with these exceptions: a Yes/No question takes its No answer; a Send Email screen sends the email unreviewed when it has a recipient, subject and body (skipped otherwise); Import File and Export File still import or export, only their summary is skipped. A later step that needs a skipped step's record makes the run fail.
- It changes records; the user confirms before it runs.

**When to use which:** `execute-pathway` for an all-automatic pathway (a sync, a recalculation, an "(auto)" pathway) when only the end result matters, and with `--skip-screens` when running it the way a scheduled pathway or integration would is the point. `pathway-run start` for everything else, and also for an automatic pathway when the user wants to see each step or where the web app would land.

**Output**

```
Ran pathway 25928 "Send Job to Accounting" on record 6a9056455ff33756afba5084 (Job 26-0050)
```

- The line means the run finished every step it reached; it doesn't list which steps ran (`get-record`, `get-history-by-record --record-id <id>` show what changed).
- A run that didn't finish, including one where a step failed, is an error (non-zero exit), never this line.

**Common errors**

- A pathway with screen steps, run without `--skip-screens` (exit 3, nothing ran):

  ```
  NOT RUN: Pathway 25927 "Add Job Notes" has a step that shows a screen in the web app. execute-pathway runs pathways on the headless runner, which shows no screens:
    - "Add Job Notes" (Post Note step 230414): skipped
  pathway-run start --pathway-id 25927 --record-id 6a9056455ff33756afba5084 runs it step by step, with that screen filled in.
  The same command with --skip-screens runs it on the headless runner anyway, that step handled as listed.
  Show this to the user and ask which they want.
  ```

- `<user email> can't start "<pathway>" on record <id>:` followed by `✗ <check>: <detail>` lines; `(only the visibility criteria block it: a Designer can run it with --mode super-user)` when that's the only one. `pathway-run map --pathway-name "<name>" --record-id <id>` lists the checks; `pathway-run available --record-id <id>` the pathways the user can start there.
- `Pathway <id> "<name>" stopped on record <id> at "<step title>" (<step type> step <step id>): the step needs a person (a question, a form or a choice), so the pathway can't run headlessly; pathway-run start runs it step by step.`: the server refused a step it couldn't skip and stopped there. Steps before it already ran and their changes are saved.
- `Pathway <id> "<name>" stopped on record <id> at "<step title>" (<step type> step <step id>) without finishing: <reason>. get-history-by-record --record-id <id> shows what it changed before it stopped.`: a step failed and the run stopped there; steps before it already ran and their changes are saved. A step of a pathway this one launched shows without its type; a run the server answers without the step reads `… stopped on record <id> without finishing. …`.
- `Pathway "X" not found — did you mean "…"?` / `Ambiguous pathway name "X"`: use `--pathway-id` (`pathway-run available --record-id <id>` lists a record's pathway ids and names, `pathway-run available --from dashboard` the dashboard's).
- `✗ runnable: the pathway has validation errors` (or `has no steps`) in that refusal: the Designer has to fix the pathway; the web app offers it to no one.

**Related:** `pathway-run start`, `pathway-run map`, `pathway-run available`, `run-step` (one workflow step, not a pathway).

Related commands: [`pathway-run start`](pathway-run-start.md), [`pathway-run submit`](pathway-run-submit.md), [`pathway-run act`](pathway-run-act.md), [`pathway-run cancel`](pathway-run-cancel.md), [`run-step`](run-step.md).
