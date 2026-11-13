# pathway-run start

Runs with: path_run_pathway(command: "pathway-run start", args: "<flags>") (change).

```
Usage: pathway-run start [options]

Start a pathway run, exactly as the web app runs it; stops at the first screen
that needs input

Options:
  --pathway-id <id>          Pathway ID
  --pathway-name <name>      Pathway name (resolved to ID)
  --record-id <id>           Starting record ID
  --copy-of <id>             Start on a fresh copy of this record (its editable
                             fields and links), made just for this run
  --copy-set <Field=value>   With --copy-of: a field value for the copy
                             (repeatable) (default: [])
  --task-id <id>             Launch by completing this task on --record-id
                             (Complete Task steps complete it)
  --folder-id <id>           Launch from this folder's header
  --from <point>             Where it is launched: record | dashboard | folder
                             | task (default: from the other flags)
  --mode <mode>              real-world (default: only launches the user could
                             make in the web app) | super-user (also when the
                             pathway's visibility criteria don't match the
                             record; Designer only, this run only)
  --as-user <id|email|name>  Run as another account member (admin impersonation
                             session)
  --tag <tag>                Pre-run tag choice for created records: "<tag
                             name|id>" or "<group id>=<tag>" (repeatable)
                             (default: [])
  --answers <file|json>      Scenario: JSON array of scripted answers, consumed
                             in order
  --strict                   Fail (exit 4) instead of pausing when a screen has
                             no usable scripted answer
```

## Examples

- args: `--pathway-name "Win Job" --record-id 6a9056455ff33756afba5084`

## Notes

Starts a pathway run exactly as the web app runs it: automatic steps run, and the run stops at the first screen that needs a person.

**Examples**

```
pathway-run start --pathway-name "Record Deposit" --record-id 6a9056455ff33756afba5084
pathway-run start --pathway-id 41674 --from dashboard
pathway-run start --record-id 6a9056455ff33756afba5084 --task-id 9071
pathway-run start --pathway-name "New Job" --tag "Austin"
pathway-run start --pathway-name "Record Deposit" --record-id 6a9056455ff33756afba5084 --answers '[{"surface":"edit-record","set":{"Deposit Amount":"1500"}}]'
```

- `--record-id` the starting record; `--from record|dashboard|folder|task` (or `--folder-id`, `--task-id`) the launch point, checked like the web app's.
- `--task-id` with `--record-id` runs the task's pathway; a Complete Task step in it closes the task.
- `--tag` picks the tag a created record gets when the user holds several in a group.
- `--answers` scripts the screens to come (`--strict`: fail with exit 4 instead of pausing).
- `--mode super-user` (Admins and Designers): starts even when the pathway's visibility criteria don't match the record.

**Output:** the pathway (with its context), each step as it runs, then the paused screen, or how the run ended:

```
━━ Waiting for input · step "Enter Deposit" [EditRecord] · screen: edit-record ━━
  Step context: Record the deposit the customer paid.
Enter Deposit
  on Job 26-0074 (6a9056455ff33756afba5084)
  Editable fields:
  [191676] Deposit Amount (Currency, required) = (blank)
  [191674] Deposit Method (Selection) = (blank)
        options: "Check", "Credit Card", "Wire"

Next:
  pathway-run submit --set "Deposit Amount=<value>"
  pathway-run submit   (save unchanged)
  pathway-run cancel
(run 3f2b8c1e-5d47-4a8e-9b1c-2e6f0a9d7c34)
```

- Lines to read: the header names the step, its type and the screen (`screen: <surface>`); the screen lists what it needs (fields with type, required, current value, every option, context; numbered results with their ids; file types and the default); `Next:` gives the exact commands it takes right now; `(run <id>)` is the run's id.
- Ended: `✔ Pathway "<name>" finished.` (with `→ the web app would now open record <id>` when it would), `✗ Pathway "<name>" failed at step "<title>" (<id>): <message>`, then a line with the request and screen counts and `log: pathway-run log --run <id>`.
- How to answer each screen: the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [pathway-screens.md](../pathway-screens.md).

**Common errors**

- `<user email> can't start "<pathway>" from <point>`, followed by the launch checks that failed (role, where it's shown, starting business object, record access, visibility criteria), one `✓`/`✗` line per check. `pathway-run available --record-id <id>` lists the ones the user can start there.
- `Task … has no pathway`: complete it with `complete-task`.

**Related:** `pathway-run available`, `pathway-run map` (what it will ask, before running), `pathway-run submit`, `pathway-run show`, `pathway-run cancel`, `execute-pathway` (headless).

Related commands: [`pathway-run submit`](pathway-run-submit.md), [`pathway-run act`](pathway-run-act.md), [`pathway-run cancel`](pathway-run-cancel.md), [`execute-pathway`](execute-pathway.md), [`run-step`](run-step.md).
