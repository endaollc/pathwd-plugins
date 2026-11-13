# path-milestone-explain

> **Commands:** every command in this skill is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../../pathwd-guide/references/commands.md) gives any command's syntax and the tool that runs it.

Why a record is (or isn't) in one milestone: the milestone's criteria, printed as a Logic Builder formula, checked clause by clause against the record's current field values, workflow steps, milestones, archived state and linked records.

Milestones are worked out by PATH from the record's workflow steps and field values whenever the record changes (the [`path-concepts` guide](../../pathwd-guide/references/concepts.md)'s [workflow.md](../../pathwd-guide/references/concepts/workflow.md)). The criteria are Designer work: this explanation reads them and changes nothing.

## What the user gives

1. **The record**: its 24-character id, or what they call it ("Job 1347", "the Main St job"), found with `search`.
2. **The milestone**: its name ("Lead", "Ready to Order"). One milestone per explanation; for two, explain each in turn.

When either is missing, it is asked for first.

## Commands, in order

```
search --query "1347" --bo-name Job
get-record --record-id 665f1c2e9b1d4a0087654321
list-workflow-milestones --bo-name Job
get-filter milestone 8820 --bo 5050
get-record --record-id 665f1c2e9b1d4a0087654321 --raw
list-workflow-steps --bo-name Job
```

1. **The record.** `search` finds it by name or number (several hits: the user picks one, each shown as `<identifier> (<id>)`). `get-record` gives its identifier, business object, current **milestones by name**, and its field values by name as the record viewer shows them. Its header ends in `[archived]` when it is archived.
2. **The milestone.** `list-workflow-milestones` gives each milestone's id and name; the `flags` column says `initial` (the default milestone) or `disabled` (turned off by the designer). Names match ignoring case; a name that matches none: the user picks from the list.
3. **The criteria.** `get-filter milestone <milestone id> --bo <business object id>` prints them as a formula, e.g. `[Need to Schedule Toggle] = TRUE OR HASSTEP("Need to Schedule")`. `(no filter)` on the initial milestone means every record enters it when created.
4. **Steps and stored values.** `get-record --raw` gives the `steps:` line (the record's active workflow steps, by name with ids) and the stored values (percent as a fraction, dates in UTC); `list-workflow-steps` lists every step of the business object. Needed only when the formula has `HASSTEP` / `NOTHASSTEP` / step dates, or a comparison the display value can't settle.

## Checking each clause

Split the formula at its `AND` / `OR` (parentheses group), and check every clause against the record. What each function and operator means, including how empty values count: the [`path-logic-formula-syntax` guide](../../pathwd-find-records/references/logic-formula-syntax.md) and the [`path-cli` guide](../../pathwd-guide/references/commands.md)'s [find-records-syntax.md](../../pathwd-find-records/references/find-records-syntax.md).

| Clause | Checked against |
|---|---|
| `[Field] = "x"`, `>`, `ISANYOF(…)`, `CONTAINS(…)`, `ISEMPTY(…)` | the field's value in `get-record` (or `--raw` for the stored form) |
| `[Relationship.Field] …` | the linked record's value (`list-related-records --record-id <id> --relationship-name <name>`, or `get-record` on the linked record) |
| `HASSTEP("s")` / `NOTHASSTEP("s")` | whether the step is on the `steps:` line (`get-record --raw`) |
| `INMILESTONE("m")` / `NOTINMILESTONE("m")` | the record's current milestones (`get-record`) |
| `STEPFIRSTOCCURRED("s")`, `MILESTONEFIRSTENTERED("m")` and the other entry dates | the record's history (`get-history-by-record --record-id <id>`) |
| archived | `[archived]` in `get-record`'s header |

A clause on a value the record's output doesn't include can't be checked here: it is reported as not checked, without guessing.

- A negative rule (`!=`, `ISNOTANYOF`, `NOTCONTAINS`) holds when the field is empty (never set or cleared), except a pick-list rule that lists `BLANK`, which needs a value. Through a relationship, a record with no linked record fails every rule except `ISEMPTY`.
- `AND` holds when every clause holds; `OR` when any does.
- A disabled milestone (`disabled` in the `flags` column) holds no records whatever its criteria say.

## The answer

Lead with one line: the record, the milestone, and whether the record is in it now (from `get-record`'s milestones). Then one or two plain sentences on why, then the clauses:

```
Service SR26-00030 (6ab15867998ea2c27b374a34) · milestone "Need to Schedule" (17180) · in it now: YES

It is in Need to Schedule because the Need to Schedule step is on it; the toggle is off.

OR  →  TRUE
  ✗ [Need to Schedule Toggle] = TRUE        the record has: No
  ✓ HASSTEP("Need to Schedule")             the step is on the record
```

- Names, never bare ids: the record as `<identifier> (<id>)`, steps and milestones by name, each field with its value on the record.
- When the clauses say one thing and the record's milestones another (the criteria hold but the record isn't in the milestone, or the reverse), say so plainly: milestones are re-checked when the record changes, so a record that hasn't changed since the criteria were edited can still show the old result.
- What would make the record move ("recording the Job Won step would take it out of Lead") is useful to say. Changing the record is a separate request the user makes; changing the criteria is Designer work in PATH's Designer.
