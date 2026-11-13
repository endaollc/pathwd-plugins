# parse-filter

Runs with: path_data_model(command: "parse-filter", args: "<flags>") (read-only).

```
Usage: parse-filter [options]

Validate a Logic Builder formula (e.g. '[Department] = "Retail" AND
INMILESTONE("Lead")') against a business object without saving it. Prints the
normalized formula.

Options:
  --bo-id <id>         Business object ID the rules are rooted on
  --bo-name <name>     Business object name (resolved to ID)
  --bo <id>            Same as --bo-id
  --text <formula>     Formula text
  --report             Report filter: permit
                       MILESTONEFIRSTENTERED/MILESTONELASTENTERED/STEPFIRSTOCCURRED/STEPLASTOCCURRED
                       and ? blank values
  --report-id <id>     Parse as this report's filter: implies --report, roots
                       on its business object and permits COLUMN("…") /
                       CUSTOMFIELD("…") for its custom columns and fields
  --allow-blank        Permit ? blank values (folder filters)
  --allow-entry-dates  Permit milestone/step entry-date rules
```

## Examples

- args: `--bo-name Job --text '[Department] = "Retail" AND INMILESTONE("Lead")'`

## Notes

Checks a Logic Builder formula against one business object without saving or running it: every field, option, relationship, milestone and step name is resolved, and the formula comes back as PATH reads it. Use it to check a filter before it is saved; `find-records` runs the same language against the records.

**Examples**

```
parse-filter --bo-name Job --text '[Department] = "Retail" AND INMILESTONE("Lead")'
parse-filter --bo-name Job --text 'INMILESTONE("Lead") AND [Department] = ?' --allow-blank --allow-entry-dates
parse-filter --report-id 2479 --text 'MILESTONELASTENTERED("Full Quote Out") >= DATE("2026-11-01")'
```

- `--bo-name` / `--bo-id` (`--bo` is the same) is the business object the rules are rooted on: a folder's or report's business object, or a milestone's or task's.
- Without options it checks the rules every filter allows (milestone and task criteria, Fork conditions, visibility rules). A folder also allows `?` (filled in by the viewer) and milestone/step entry dates: `--allow-blank --allow-entry-dates`. A report: `--report`, or `--report-id <id>`, which also roots on the report's business object and allows its `COLUMN("…")` / `CUSTOMFIELD("…")` names.
- It checks names and syntax only. Whether a saved folder or report accepts the formula (its user filters, its columns): `set-filter … --dry-run`.
- The formula language (operators, functions, values, dates, blanks, relationship paths) and its common mistakes: the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md), and the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [find-records-syntax.md](../find-records-syntax.md).

**Output**

- Text: `formula: <normalized formula>` and `rules: <count>`. Read the formula back: it shows how PATH understood the text (`DATE(2026,11,1)` comes back as `DATE("2026-11-01")`; `[Job Name] = ""` comes back as `[Job Name] = ?`, a rule with no value that matches every record, where `ISEMPTY([Job Name])` was meant).

**Common errors** (each with the formula and a `^^^` under the problem)

- `Unknown field "Departmnt"`, `Unknown milestone "Won"`, `Unknown step "…"`: not a name on this business object; `find-records --bo-name <bo> --catalog` lists the names.
- `? (fill in later) is only available in folder and report filters`: add `--allow-blank` (folder) or `--report`.
- `Milestone and step dates are only available in folder and report filters`: add `--allow-entry-dates` (folder) or `--report`.
- `"" is not an option of this field`: a Selection compared with `""`; "has no value" is `[Department] = BLANK`.
- `Provide --bo-id, --bo-name or --report-id`.

**Related:** `get-filter` (a saved filter as a formula), `find-records --logic` (run a formula now), `set-filter` (save a folder or report filter), `create-folder --logic`, `create-report --logic`.

Related commands: [`list-business-objects`](../../../pathwd-guide/references/commands/list-business-objects.md), [`get-business-object`](../../../pathwd-guide/references/commands/get-business-object.md), [`list-fields`](../../../pathwd-guide/references/commands/list-fields.md), [`get-field`](../../../pathwd-guide/references/commands/get-field.md), [`list-field-groups`](../../../pathwd-guide/references/commands/list-field-groups.md), [`list-relationships`](../../../pathwd-guide/references/commands/list-relationships.md), [`list-workflow-milestones`](../../../pathwd-guide/references/commands/list-workflow-milestones.md), [`list-workflow-steps`](../../../pathwd-guide/references/commands/list-workflow-steps.md), [`list-workflow-tasks`](../../../pathwd-guide/references/commands/list-workflow-tasks.md), [`list-tag-groups`](../../../pathwd-guide/references/commands/list-tag-groups.md), [`list-file-types`](../../../pathwd-guide/references/commands/list-file-types.md), [`list-appointment-fields`](../../../pathwd-guide/references/commands/list-appointment-fields.md), [`get-filter`](get-filter.md).
