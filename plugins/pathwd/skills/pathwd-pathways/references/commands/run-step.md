# run-step

Runs with: path_run_pathway(command: "run-step", args: "<flags>") (change).

```
Usage: run-step [options]

Run a workflow step on a record (triggers milestone evaluation)

Options:
  --record-id <id>  Record ID
  --step-id <id>    Workflow step ID
  --bo-id <id>      Business object ID (auto-resolved from the record if
                    omitted)
  --bo-name <name>  Business object name (resolved to ID)
```

## Notes

Runs one workflow step on a record (marks the step as done on it), the same action a pathway's Run Workflow Step step takes (formerly Perform Activity). PATH then re-evaluates the record's milestones, so the record can move into or out of milestones, and tasks and actions tied to them can follow. It is not a pathway step and not a task: a pathway is run with `pathway-run start` or `execute-pathway`, a task completed with `complete-task`.

**Examples**

```
list-workflow-steps --bo-name Job
run-step --record-id 6a9056455ff33756afba5084 --step-id 17446
run-step --record-id 6a9056455ff33756afba5084 --step-id 17444 --bo-name Job
```

- `--step-id` is a workflow step id of the record's business object (`list-workflow-steps --bo-name <name>`: id, name, milestone). Steps are named for what happened ("Job Won", "Put On Hold").
- The business object comes from the record when `--bo-id` / `--bo-name` are left out. One given is sent as is, not checked against the record, so leaving them out is the safe choice.
- It changes the record's workflow state and can set off what the account ties to milestones (for example tasks created when a record enters one). The user confirms before it runs.
- In the web app people run workflow steps through pathways. When a pathway does this step along with the screens and checks around it ("Put On Hold" asks for an explanation first), running the pathway keeps the record as the process expects: find it with `pathway-run available --record-id <id>` and preview it with `pathway-run map`. `run-step` is for the bare step, when the user asks for exactly that.

**Output**

```
Ran step 17446 "Job Won" on record 6a9056455ff33756afba5084 (Job 26-0050)
Milestones: Won, Ordering
```

- Lines to read: the step and record, then the milestones the record is in after the step (by name). `get-record --record-id <id>` shows the rest.

**Common errors**

- An unknown or mistyped record id fails when the record is read; a record id is 24 hex characters, and a record number or identifier ("Job 26-0050") isn't one: `search` finds it.
- Take step ids from `list-workflow-steps` for the record's own business object; the command doesn't check that the step belongs to it.

**Related:** `list-workflow-steps`, `list-workflow-milestones`, `get-record`, `pathway-run available`, `execute-pathway`. Why a record is or isn't in a milestone: the [`path-milestone-explain` guide](../../../pathwd-history/references/milestone-explain.md).

Related commands: [`pathway-run start`](pathway-run-start.md), [`pathway-run submit`](pathway-run-submit.md), [`pathway-run act`](pathway-run-act.md), [`pathway-run cancel`](pathway-run-cancel.md), [`execute-pathway`](execute-pathway.md).
