# Workflow: logic-builder

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATHwd each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../../../pathwd-guide/references/commands.md) gives any command's syntax and the tool that runs it.

**Covers:** Logic Builder formulas, the rules that decide which records a folder, folder sticker or report includes: writing one from a plain-language request, checking it, and saving it. Commands: `parse-filter`, `get-filter`, `set-filter`, `create-folder --logic`, `create-report --logic`. Filter JSON is never written by hand: the formula is.

This connection writes **folder and report** filters (and folder stickers). Rules for anything else — BO stickers, task auto-complete/cancel/availability, milestone entry, Fork steps, Edit Record Table filters, pathway visibility, field/field-group/relationship visibility, logic settings — are Designer work, done by an Admin or Designer in **PATH's Designer** (or with the PATH Designer plugin's `cli-logic-builder` command, if it's installed). You can still read any of them here with `get-filter`.

All syntax (comparisons, `AND`/`OR`, names, functions, operators per field type, entry dates, blanks) is in the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md).

## Workflow

### 1. Identify the target and its root business object

Figure out from the request what kind of criteria this is and where it's rooted:

| The user is describing... | Target(s) | Rooted on |
|---|---|---|
| Which records a folder shows | `folder` (existing) or `create-folder --logic` (new) | the folder's BO |
| When a folder sticker lights up | `folder-sticker` (needs `--sticker <folder sticker id>`); a new one: `create-folder-sticker --logic '<formula>'` | the folder's BO |
| Which records a report includes | `report` (existing) or `create-report --logic` (new) | the report's BO |
| Just the matching records, now — nothing saved (an advanced search) | `find-records --logic` — use the [`cli-find-records` workflow](find-records.md) | the records' BO |

Anything else (a BO sticker, task, milestone, Fork, pathway or visibility rule): that's Designer work (PATH's Designer, or the PATH Designer plugin if it's installed). To explain one, read it with `get-filter <target> <id>` and describe it.

If unclear, ask which it is — it determines what the formula is allowed to reference (only folders/reports allow `?` blanks and milestone/step entry dates).

Find the object itself with `list-folders` or `list-reports`.

### 2. Inspect names

Only look up what the formula will reference:
- `find-records --bo-name <n> --catalog` lists them all at once, in the names a formula uses: fields with their types and Selection options, relationship paths (`[Job Main Customer.Customer Name]`), milestones and steps
- or one kind at a time: `list-fields --bo-name <n>` (the `name`, `type` and `options` columns; `get-field --field-id <id>` when an option list is cut with `…`), `list-workflow-milestones --bo-name <n>` and `list-workflow-steps --bo-name <n>` (the `name` column), `list-relationships --bo-name <n>` (`name` and `to`, for related-BO paths)

Match names case-insensitively; if ambiguous, list the candidates and ask.

### 3. Write the formula

Compose the formula per the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md) reference. Keep it as close to the user's own words as the syntax allows; don't invent conditions they didn't ask for. There is no `NOT(...)`: negate a rule with its own negative form (`NOTHASSTEP`, `NOTINMILESTONE`, `ISNOTANYOF`, `NOTCONTAINS`, `!=`; the full table is in the syntax skill).

### 4. Validate

```
--account <ID> parse-filter --bo-name <root BO> --text '<formula>'
```

Add `--report` for report targets, or `--allow-blank --allow-entry-dates` for folder targets, to match what that target permits. Fix and re-check until it parses clean.

### 5. Confirm with the user

Present the result in **plain language first**, then the formula — never the stored filter:

> "Here's what I'll set: Jobs where Department is Retail AND the job is in the Qualified milestone.
>
> `[Department] = "Retail" AND INMILESTONE("Qualified")`
>
> Save this?"

Do not save until the user confirms.

### 6. Save

- Existing object: `--account <ID> set-filter <target> <id> --text '<formula>'` (try `--dry-run` first if you want to inspect the pipeline before writing).
- New folder: `--account <ID> create-folder --bo-name <n> --folder-name "<name>" --logic '<formula>' ...` (see the [`cli-folders-skill` workflow](folders.md) for the rest of folder creation — permissions, columns).
- New report: `--account <ID> create-report --bo-name <n> --name "<name>" --columns '<columns>' --logic '<formula>' ...` — the formula can compare the report's own calculated columns and Custom Fields (`COLUMN("…")`, `CUSTOMFIELD("…")`); see the [`cli-reports-skill` workflow](reports.md) for the rest (columns, grouping, context, sharing).

`--logic-raw <json>` exists on `create-folder` as an escape hatch for the rare filter the formula language can't express (check with `get-filter`, which prints `UNSUPPORTED(...)` for such rules).

### 7. Confirm

`--account <ID> get-filter <target> <id>` (or `list-folders` for a newly created folder) to show the saved formula back to the user.

## Key facts and confirmations

- Every command here runs through the PATHwd tool for its area (each command's page in [the command index](../../../pathwd-guide/references/commands.md) names it).
- Names (fields, milestones, steps, roles) resolve to ids through the commands; ids aren't guessed.
- Saving a filter changes which records a folder, sticker or report shows for everyone who uses it: the user confirms the rule, restated in plain words, first.
- Users know "the rules for which records show up", not "GroupFilter" or "filter_condition".
- A stored filter that prints `UNSUPPORTED(...)` can't be edited as a formula: it is changed in PATH's Designer, or replaced as a whole (`set-filter --clear`, then a new formula).

Full syntax, targets, examples and error table: the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md). Field formulas (calculations) use a sister syntax set in PATH's Designer.
