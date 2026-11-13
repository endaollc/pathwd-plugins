# get-filter

Runs with: path_data_model(command: "get-filter", args: "<flags>") (read-only).

```
Usage: get-filter [options] <target> <id>

Print a stored Logic Builder filter as formula text (milestone criteria, task
criteria, Fork conditions, folder/report/sticker filters, visibility rules).
Folder / report: each rule ticked as a user filter follows as `user filter
"Label": <rule>` (a milestone "currently in" rule with ` allowed: "A", "B"`,
the milestones viewers may pick); set-filter --user-filter takes the line back;
the `  uid: …` line under it is the row's uid, which run-report / list-records
--user-filter values can name it by.

Arguments:
  target          Where the filter lives (see Targets below)
  id              Id of the object holding the filter

Options:
  --bo <id>       Business object ID (task targets)
  --pathway <id>  Pathway ID (fork, table-filter)
  --sticker <id>  Folder sticker ID (folder-sticker)
```

## Notes

Prints a saved Logic Builder filter as a formula: a milestone's criteria, a task's auto-complete, auto-cancel or availability rules, a Fork step's condition, a folder's or report's filter, a sticker's criteria, a visibility rule. The answer to "what puts a Job in Full Quote Out?" or "what does this folder show?".

**Examples**

```
get-filter milestone 17106
get-filter task-complete 5838 --bo 5050
get-filter folder 21051
get-filter report 2478
get-filter fork 337564 --pathway 31547
```

- The first word names where the filter lives and the id is that object's: a milestone id (`list-workflow-milestones`), a task template id (`list-workflow-tasks`), a folder or report id (`list-folders`, `list-reports`), a pathway step's `step_id` (`pathway-run map --pathway-id <id>`). `--help` lists every target with the id it takes.
- `task-complete`, `task-cancel` and `task-filter` need `--bo <business object id>`; `fork` and `table-filter` need `--pathway <pathway id>`; `folder-sticker` needs `--sticker <folder sticker id>`.
- Reading is open for every target. Of these, only folder, folder-sticker and report filters can be changed with these commands (`set-filter`); the others are Designer work.
- Reading the formula (functions, `?`, entry dates, `CURRENTUSER()`, `SETTING()`): the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md) and the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [find-records-syntax.md](../find-records-syntax.md).

**Output**

- Text: the formula, e.g. `HASALLSTEPS("Full Quote Sent to Customer") AND NOTHASSTEP("Job Lost") AND NOTHASSTEP("Job Won") AND …`, or `(no filter)` when none is set.
- A folder or report adds one line per rule viewers can change in the filter bar, each followed by the row's uid:

  ```
  ISANYTIME([Created Date]) AND [Project Outside Salesperson.Person Name] = CURRENTUSER()
  user filter "Created Date": ISANYTIME([Created Date])
    uid: ac3yy13t
  ```

  A milestone rule ends with the milestones viewers may pick, `allowed: "Lead", "Qualified"`. `set-filter --user-filter` takes a `user filter` line back as it is; `run-report` / `list-records --user-filter` values name a row by its label or by that uid (`[{"uid":"ac3yy13t","values":[…]}]`).
- `UNSUPPORTED("…")` marks a stored rule the formula language can't express (with a note on stderr); such a filter is changed in PATH's Designer only.

**Common errors**

- `No milestone with id 99999999.` (and the same for every target): the id isn't one of that kind; check the list it comes from.
- `The task-complete target needs --bo <id>.`
- `Unknown target "mile". Targets: milestone, task-complete, …`.
- `Step 337564 (fork) has no FilterWithFolderConfiguration configuration.`: the target doesn't match the step's type (`fork` for a Fork step, `table-filter` for an Edit Record Table step).

**Why is a record (not) in a milestone?** Compare the milestone's formula with the record (`get-record --record-id <id> --raw` lists its `steps` and field values); the [`path-milestone-explain` guide](../../../pathwd-history/references/milestone-explain.md) walks it rule by rule.

**Related:** `parse-filter` (check a formula), `find-records --logic` (run one), `set-filter` (change a folder or report filter), `list-workflow-milestones`, `list-workflow-tasks`.

Related commands: [`list-business-objects`](../../../pathwd-guide/references/commands/list-business-objects.md), [`get-business-object`](../../../pathwd-guide/references/commands/get-business-object.md), [`list-fields`](../../../pathwd-guide/references/commands/list-fields.md), [`get-field`](../../../pathwd-guide/references/commands/get-field.md), [`list-field-groups`](../../../pathwd-guide/references/commands/list-field-groups.md), [`list-relationships`](../../../pathwd-guide/references/commands/list-relationships.md), [`list-workflow-milestones`](../../../pathwd-guide/references/commands/list-workflow-milestones.md), [`list-workflow-steps`](../../../pathwd-guide/references/commands/list-workflow-steps.md), [`list-workflow-tasks`](../../../pathwd-guide/references/commands/list-workflow-tasks.md), [`list-tag-groups`](../../../pathwd-guide/references/commands/list-tag-groups.md), [`list-file-types`](../../../pathwd-guide/references/commands/list-file-types.md), [`list-appointment-fields`](../../../pathwd-guide/references/commands/list-appointment-fields.md), [`parse-filter`](parse-filter.md), [`list-records`](../../../pathwd-guide/references/commands/list-records.md), [`run-report`](run-report.md), [`set-filter`](set-filter.md).
